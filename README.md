# Heroku Buildpack for libpostal

This buildpack installs [libpostal](https://github.com/openvenues/libpostal), a C library for parsing/normalizing street addresses around the world, on Heroku applications.

## Usage

### Adding the Buildpack

Add this buildpack to your Heroku application:

```bash
heroku buildpacks:add https://github.com/yourusername/heroku-buildpack-libpostal
```

If you're using multiple buildpacks, you should add this one before your main language buildpack:

```bash
heroku buildpacks:add --index 1 https://github.com/yourusername/heroku-buildpack-libpostal
```

### Configuration

This buildpack will:

1. Download and compile libpostal
2. Install the library and headers
3. Download the required data files
4. Set up appropriate environment variables

After deployment, your application will have:

- libpostal libraries available in `/app/.heroku/vendor/libpostal/lib`
- Header files in `/app/.heroku/vendor/libpostal/include`
- Data files in `/app/.heroku/vendor/libpostal/data`
- The `libpostal_data` utility in `/app/.heroku/vendor/libpostal/bin`

The following environment variables will be set:
- `LD_LIBRARY_PATH` includes `/app/.heroku/vendor/libpostal/lib`
- `LIBRARY_PATH` includes `/app/.heroku/vendor/libpostal/lib`
- `CPATH` includes `/app/.heroku/vendor/libpostal/include`
- `LIBPOSTAL_DATA_DIR` set to `/app/.heroku/vendor/libpostal/data`
- `PATH` includes `/app/.heroku/vendor/libpostal/bin`

### Using with Different Languages

#### Python

For Python applications, you'll typically use the [pypostal](https://github.com/openvenues/pypostal) package:

```python
from postal.parser import parse_address
from postal.expand import expand_address

# Parse an address
parsed = parse_address("781 Franklin Ave Crown Heights Brooklyn NYC NY 11216 USA")
print(parsed)

# Expand an address
expanded = expand_address("781 Franklin Ave Crown Hts Brooklyn")
print(expanded)
```

Make sure to include `pypostal` in your `requirements.txt`:

```
pypostal==1.0.0
```

#### Ruby

For Ruby applications, use the [ruby_postal](https://github.com/openvenues/ruby_postal) gem:

```ruby
# For parsing addresses
require 'ruby_postal/parser'
parsed = Postal::Parser.parse_address("781 Franklin Ave Crown Heights Brooklyn NYC NY 11216 USA")
puts parsed

# For expanding addresses
require 'ruby_postal/expand'
expanded = Postal::Expand.expand_address("781 Franklin Ave Crown Hts Brooklyn")
puts expanded
```

Add the gem to your Gemfile:

```ruby
gem 'ruby_postal'
```

#### Node.js

For Node.js applications, you can use the [node-postal](https://github.com/openvenues/node-postal) package:

```javascript
const postal = require('node-postal');

// Parse an address
const parsed = postal.parser.parse_address("781 Franklin Ave Crown Heights Brooklyn NYC NY 11216 USA");
console.log(parsed);

// Expand an address
const expanded = postal.expand.expand_address("781 Franklin Ave Crown Hts Brooklyn");
console.log(expanded);
```

Add the package to your package.json:

```json
{
  "dependencies": {
    "node-postal": "^1.0.0"
  }
}
```

## Notes

- The first deployment might take longer due to the compilation of libpostal and downloading of data files.
- The libpostal data files are quite large (around 2GB), which will increase your slug size significantly.
- You might need to adjust your dyno size to accommodate the memory requirements of libpostal.

## License

This buildpack is released under the MIT License. libpostal is released under the MIT License.