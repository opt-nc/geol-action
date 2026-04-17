# Geol Action

![GitHub License](https://img.shields.io/github/license/opt-nc/geol-action)


GitHub Action to install [geol](https://github.com/opt-nc/geol) in your GitHub Actions workflows.

## 📖 About

`geol` is a Go-based CLI tool to efficiently manage and monitor End Of Life (EOL) dates for various products and technologies, directly from your terminal and CI/CD pipelines, using the [endoflife.date](https://endoflife.date/) API.

This GitHub Action makes it easy to integrate `geol` into your workflows to automate EOL version checks for your dependencies and tech stack.

## 🚀 Usage

### Basic example

```yaml
name: Check EOL

on: [push, pull_request]

jobs:
  check-eol:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd

      - name: Install geol
        uses: opt-nc/geol-action
        with:
          version: 'v2.12.1' # Optional: specify a version or use 'latest'

      - name: Check stack EOL
        run: |
          geol check
```

## 🔧 How it works

This action:
1. Clones the [opt-nc/geol](https://github.com/opt-nc/geol) repository
2. Runs the `install.sh` script
3. Cleans up temporary files
4. Makes `geol` available for the next workflow steps

## 📚 geol Documentation

For more information on using `geol`, see:

- [Official geol documentation](https://opt-nc.github.io/geol/)
- [geol GitHub repository](https://github.com/opt-nc/geol)
- [endoflife.date API](https://endoflife.date/docs/api/v1/)

## 🤝 Contributing

Contributions are welcome! Feel free to open an issue or pull request.

## 📄 License

This GitHub Action is distributed under the Apache-2.0 license. See the [LICENSE](LICENSE) file for details.

