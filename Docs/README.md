# Overview

This explains how to view and contribute to the documentation for MicroEleX.

## Viewing the documentation on the web

See https://amrex-microelectronics.github.io

## Contributing to the documentation

Obtain the source files for the documentation
```
git clone https://github.com/AMReX-Microelectronics/AMReX-Microelectronics.github.io.git
cd AMReX-Microelectronics.github.io/Docs
```
If you already have this git repository, simply update the repository instead
```
cd AMReX-Microelectronics.github.io/Docs
git pull
```

Install the Python requirements for compiling the documentation:
```
python3 -m pip install -r requirements.txt
```

Edit any .rst files in ./source/ to update the documentation. Then

```
make clean
make html
```
You can then open the file `build/html/index.html` with a standard web browser (e.g. Firefox), in order to visualize the results on your local computer.
Then, commit and push the changes.
```
git commit -a
git push
```

This will also update the https://amrex-microelectronics.github.io website.
