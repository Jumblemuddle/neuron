# Contributing to neuron

This document describes how to develop neuron, as well as some guidelines when it comes to submiting a PR.

## Developing

When modifying the source code, use `bin/run` (which uses ghcid) to test your changes in real-time:

```bash
bin/run -d $(pwd)/guide rib -wS
```

This command automatically recompiles and restarts when you change any of the Haskell source files. Furthermore, this command runs site generation on the given Zettelkasten. You can pass the same neuron arguments to `bin/run`. This is essentially equivalent to running a development version of neuron with instant reload.

### Running tests

Unit tests can be run via ghcid as follows:

```
bin/test
```

### Developing on reflex-dom-pandoc

Neuron delegates HTML rendering of the Pandoc AST to [reflex-dom-pandoc](https://github.com/srid/reflex-dom-pandoc). To hack on it, first [install Obelisk](https://github.com/obsidiansystems/obelisk#installing-obelisk) and then:

```sh
# This will clone the git repo of reflex-dom-pandoc at dep/reflex-dom-pandoc
ob thunk unpack dep/reflex-dom-pandoc

# Let's work on that repo
cd dep/reflex-dom-pandoc
```

For `nix-build` and `nix-shell` to still work while a dependency is unpacked, you need to change the source patch in `project.nix` to e.g. `reflex-dom-pandoc = ./dep/reflex-dom-pandoc`.

Then you can try your changes with
```
# Run ghcid (using neuron's nix config)
nix-shell ../../shell.nix --run ghcid
```

Now as you edit the reflex-dom-pandoc sources, ghcid should give you compiler feedback. Once you are done with your changes, simply re-run neuron's ghcid or bin/run (see further above) and it should reflect your changes.

When you are done, commit your changes to reflex-dom-pandoc (presumably in a branch) and then `git push` it. Finally, you must "pack" the thunk and commit the changes to the neuron repo:

```sh
cd ../..  # Back to neuron
rm -rf dep/reflex-platform/dist-newstyle # cleanup build artifacts before packing
ob thunk pack dep/reflex-dom-pandoc
git add dep/reflex-dom-pandoc
```

Don‘t forget to revert the temporary changes to your `project.nix`.

## Guidelines when submitting a PR

### Autoformatting

I autoformat every Haskell source using [ormolu](https://github.com/tweag/ormolu). Before opening a PR you are encouraged to do the same. However if you have trouble installing ormolu for whatever reason, don't let that prevent you from contributing.

### Test your build

Run `nix-build` with your changes to make sure that everything compiles, and the tests succeed.
