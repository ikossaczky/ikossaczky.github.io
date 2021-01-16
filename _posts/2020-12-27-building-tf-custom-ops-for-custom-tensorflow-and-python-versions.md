---
layout: post
title:  "Building TF custom ops for custom tensorflow and python versions"
categories: tensorflow
---

Following the [instructions from the tensorflow team][custom-op-repo], building tensorflow custom operation inside a docker container works out of the box for TF 2.4. But if this custom op is needed for a custom tensorflow and custom python version, some issues need to be addressed. Here I will summarize the steps needed to successfully built tensorflow example "zero out op" for TF 1.15, python 3.7, without GPU support (but the steps can probably be generalized for other setups also):
1. Following the [instructions from the tensorflow team][custom-op-repo] run (installed docker is needed):
    ```
    docker pull tensorflow/tensorflow:custom-op-ubuntu16
    docker run -it tensorflow/tensorflow:custom-op-ubuntu16 /bin/bash
    ``` 
    and clone the repo
    ```
    git clone https://github.com/tensorflow/custom-op.git
    ```
   Alternatively, you can clone my [fork of the custom-op-repo](https://github.com/ikossaczky/custom-op), where I included also the inner product operation with gradient computation from the [repo of David Stutz](https://github.com/davidstutz/tensorflow-cpp-op-example).

2. If python version other than 3.6 (here 3.7) is desired, install it:
   ```
   apt update
   apt install python3.7
   ```
   Make `python3` point on `python3.7`:
   ```
   rm /etc/alternatives/python3
   ln -s /usr/local/bin/python3.7 /etc/alternatives/python3
   ```

3. Fixing pip. At this point my pip3.7 was broken. To fix it, following [a comment in this post](https://stackoverflow.com/questions/44967202/pip-is-showing-error-lsb-release-a-returned-non-zero-exit-status-1/48245920), run 
   ```
   vim /usr/bin/lsb_release
   ```
   and edit the first line to be `#!/usr/bin/python3.7 -Es`. You can then try to run `pip3.7 list` which should now output no errors.
   Now, make `pip3` point on `pip3.7`:
   ```
   rm  /usr/local/bin/pip3
   ln -s /usr/local/bin/pip3.7 /usr/local/bin/pip3
   ```
   Finally, upgrade pip (this is important to be able to install tensorflow 1.15)
   ```
   pip3 install --upgrade pip
   ```

4. Install desired version of tensorflow (I tried 1.15):
   ```
   pip3 install tensorflow==1.15
   pip3 install tensorflow-cpu==1.15
   ``` 
5. Before building the package another issue needs to be fixed -the tensorflow 2.1 needs to be removed from the requirements of the new pip package. Execute
```
vim /custom-op/setup.py
```
    and change the lines
```
    REQUIRED_PACKAGES = [
    'tensorflow >= 2.1.0',
]
```
to 
```
REQUIRED_PACKAGES = [
    'tensorflow >= 1.15.0',
]
```
(or other version that you have installed). This is not needed if you use my fork of the repo and TF version >=1.15.
6. Finally, following the [custom op repo][custom-op-repo], build the package. At first, execute:
   ```
   cd /custom-op
   ./configure.sh
   ```
   Do not forget to answer the last question with "n", otherwise the operation will be built for tensorflow 2.x. After that, run 
   ```
   bazel build build_pip_pkg
   bazel-bin/build_pip_pkg artifacts
   ```
   Now, the pip package is ready in folder `/custom-op/artifacts` and can be installed with
   ```
   pip3 install artifacts/*.whl
   ```
 7. Now you can test the op directly in docker container by running 
```
cd /
python3
```
and executing the following code chunk:
    ```python
    import tensorflow as tf
    import tensorflow_zero_out
    x=tf.constant([[1,2], [3,4]])
    y=tensorflow_zero_out.zero_out(x)
    with tf.Session() as sess:
        print(sess.run(y))
    ```
The output should be
```
[[1 0]
[0 0]]
```
You can also copy the package using 
```
docker cp <container-id>:/custom-op/artifacts <path-where-to-copy-the-package>
```
to install and test it locally.

Thats all. These steps were successfully applied to build the custom zero-out operation for python 3.7, tensorflow 1.15 and without GPU support. For other setups, or after update of the `tensorflow/tensorflow:custom-op-ubuntu16` image, different issues may of course appear.

[custom-op-repo]: https://github.com/tensorflow/custom-op
