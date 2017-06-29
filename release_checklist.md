# Release checklist

- [ ] 1. Check that version numbers in `macosko2015/macosko2015/__init__.py` and `macosko2015/setup.py` match
- [ ] 2. Add release notes in `README.md`
- [ ] 3. Copy release notes to `macosko2015/HISTORY.rst`

- [ ] 4. Check that the `.rst` files are valid using [`rstcheck`](https://pypi.python.org/pypi/rstcheck/0.5) and the built-in Python RST checker:

```
python setup.py check --restructuredtext
rstcheck README.rst
rstcheck HISTORY.rst
```

- [ ] 5. Create an annotated tag for git and push it:

```
git tag -a v0.2.1 -m "v0.2.1 - Release *with* requirements.txt"
git push origin v0.2.1
```

- [ ] 6. (Optional, only if you messed up) and made the tag too early and later made changes that should be added to the tag, remove the remote tag and force re-add the local tag:

```
git push origin :refs/tags/v0.2.1
git tag -fa v0.2.1
git push --tags
```


- [ ] 7. Do a test run of uploading to PyPI test. This assumes you have set up your PyPI configuration according to [this](http://peterdowns.com/posts/first-time-with-pypi.html).
```
python setup.py sdist upload -r pypitest
```

- [ ] 8. (Optional, if there's an error) in either the `HISTORY.rst` or `README.rst` file, you will get an error. To narrow down this error, look for the corresponding line in `macosko2015.egg-info`. It won't be exactly the same line because of the header but it will be closer since `PKG-INFO` concatenates your `README.rst` and `HISTORY.rst` files.

```
warning: unexpected indent on line 615 blah blah I'm pypi RST and I'm way too strict
```

- [ ] 9. Check the [PyPI test server](https://testpypi.python.org/pypi) and make sure everything is there and the RST is rendered correctly.
- [ ] 10. Do a test installation in a `conda` environment using the PyPI test server

```
conda create --yes -n macosko2015_pypi_test_v0.2.1 --file conda_requirements.txt
# Change to a different directory to make sure you're not importing the `macosko2015` folder
# Use "pushd" instead of "cd" so it's easy to pop back to the macosko2015 folder
pushd $HOME
source activate macosko2015_pypi_test_v0.2.1
pip install --index-url https://testpypi.python.org/pypi macosko2015 --extra-index-url https://pypi.python.org/simple
```

- [ ] 11. Make sure that the correct `macosko2015` from the right environment is getting referenced. `which macosko2015` should have the following output:

```
$ which macosko2015
/Users/olga/anaconda3/envs/macosko2015_pypi_v0.2.1/bin/macosko2015
```

- [ ] 12. Check that the installation was successful. `macosko2015 -h` should have the following output:

```
$ macosko2015 -h
usage: macosko2015 [-h] {index,validate,psi} ...

Calculate "percent-spliced in" (Psi) scores of alternative splicing on a *de
novo*, custom-built splicing index

positional arguments:
  {index,validate,psi}  Sub-commands
    index               Build an index of splicing events using a graph
                        database on your junction reads and an annotation
    validate            Ensure that the splicing events found all have the
                        correct splice sites
    psi                 Calculate "percent spliced-in" (Psi) values using the
                        splicing event index built with "macosko2015 index"

optional arguments:
  -h, --help            show this help message and exit
```

- [ ] 13. Return to the original macosko2015 folder and upload to PyPI:

```
# Return to the macosko2015 directory from the `pushd`
popd
# Now upload to PyPI
python setup.py sdist upload -r pypi
```

Check that it appears correctly on the [PyPI website](https://pypi.python.org/pypi).


- [ ] 14. Upload to Bioconda:

```
cd ..
# If `bioconda-recipes` is not yet cloned, do this step. Otherwise you can
# skip it
git clone https://github.com/bioconda/bioconda-recipes
# Skip to this next line if you've already cloned bioconda-recipes
cd bioconda-recipes/recipes
git checkout -b macosko2015_v0.2.1
# Remove existing macosko2015 folders
rm -rf macosko2015
conda skeleton pypi macosko2015
git add macosko2015
git commit -m "Updated macosko2015 to v0.2.1"
git push origin macosko2015_v0.2.1
# Go up two folders to return to the parent folder
cd ../../
```

- [ ] 14. Check that a new pull request exists at [bioconda-recipes](https://github.com/bioconda/bioconda-recipes)

- [ ] 16. Upload to conda-forge

```
# Clone your forked repo of https://github.com/conda-forge/staged-recipes
git clone https://github.com/YeoLab/staged-recipes
cd staged-recipes/recipes
git checkout -b macosko2015_v0.2.1
conda skeleton pypi macosko2015
# Get md5 hash from PyPI
git add macosko2015
git commit -m "Updated macosko2015 to v0.2.1"
```

- [ ] 17. Create a [pull request](https://github.com/conda-forge/staged-recipes/compare/master...YeoLab:master?expand=1) to `conda-forge` using the YeoLab fork.
