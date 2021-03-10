GitHub Actions for DeepSpeech
=============================

While we try to stick to reusing as much as possible of standard GitHub Actions
code and actions, there are some repo-specific items we have to make use of.

Building and using a TensorFlow cache:
--------------------------------------

Because of the amount of code required to build TensorFlow, the library build
is split into two main parts to make it much faster to run PRs:
 - a TensorFlow prebuild cache
 - actual code of the library

The TensorFlow prebuild cache exists because building tensorflow (even just the
`libtensorflow_cpp.so`) is a huge amount of code and it will take several hours
even on decent systems. So we perform a cache build of it, because the
tensorflow version does not change that often.

However, each PR might have changes to the actual library code, so we rebuild
this everytime.

The `check-tensorflow_opt-macOS-cache` job checks whether such build cache
exists alrady. Those cache are stored as artifacts because of GitHub Actions
dedicatd cache size limitations.

The `build-tensorflow-macOS` job has a dependency against the cache check to
know whether it needs to run an actual build or not.

check_artifact_exists action:
-----------------------------

This action will check the existence of an artifact in the list of the repo
artifacts. Since we don't want always to download the artifact, we can't
rely on the official download-artifact action.
