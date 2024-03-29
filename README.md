# mynodepack

This repository is a template/example of how to package and publish your node packs to [PyPI](https://pypi.org). A node pack is a Python package whose subpackages are:

- divided into different folders representing categories;
- self-contained/modular;
- contain or reference special callables and other suport objects that can be used to represent nodes (visual blocks) inside the [nodezator app](https://github.com/IndiePython/nodezator).

Beware that this template does not attempt to teach you the best way or configuration for your package. It just presents **one** possible and relatively straightforward way of packaging your node pack. Teaching about packaging in Python and the many options available is not our goal here. Even so, we always welcome questions and people seeking help, so if you have any trouble just reach us on the [nodezator discussions](https://github.com/IndiePython/nodezator/discussions) or the [discord server](https://indiepython.com/discord).

Also, where relevant, we based some of this content on the information on [this link](https://packaging.python.org/en/latest/tutorials/packaging-projects/).


## Step-by-step guide

Without further ado, let's jump right into the action.


### Create a folder wherein to put your package files

The first step to create a package is to create a folder to hold all your package files. It can have any name you want, but I think it is a good practice to use the same name as your package, in this case the name of your node pack. Since in this template the node pack is called mynodepack, so it the file we used.


### Put your node pack folder inside your package folder

So, as described in [nodezator's manual](https://manual.nodezator.com), I assume you already have your node pack ready. Your node pack is represented by a folder which has one or more category folders inside and inside each category folder there are one or more folders, each containing the code for a single node.

Move or copy your node pack folder inside the package folder. At this point, your package folder will contain only your node pack folder.


### Put the metadata files inside the package folder

Now you'll put the metadata/configuration files inside package folders. Don't worry about changing them now. We'll go over each one, explained what you need to do. Just copy them into your package folder. Such files are:

- the **pyproject.toml** file, that declares what are your build system dependencies, and which library will be used to actually do the packaging;
- the license for your code: we use the **UNLICENSE**, you can use whichever license you want;
- the configuration file: **setup.cfg**;
- the **README.md**: a [markdown file](https://www.markdownguide.org/) we use to provide a description for your package and additional info/instructions about it, such as usage, installation, etc.


### Configure the metadata files

The **pyproject.toml** file can be left untouched, since it only contains instructions for the packaging software we'll use. There's no need to change it.

The **LICENSE** or **UNLICENSE** file can be any license you want. Here's a neat website to help you [choose a license](https://choosealicense.com/). The UNLICENSE file I used in software related to the [Indie Python project](https://indiepython.com) dedicates all the code to the public domain, which gives maximum freedom to users of the code. This is why it is the license I recommend. However, maybe you don't want your code in the public domain or want instead to sell licenses for your software, etc. You must consult with a lawyer depending on the complexity of your needs. Keep in mind the license you choose will have not only legal consequences, but also affect your business/non-profit/open-source project reach, so choose carefully. I'm all for keeping things open, but you might have legitimate reasons not to do so.

The **setup.cfg** configuration file is where metadata for your project resides, so you'll need to replace the values in it by data about your own package. These are the contents of the cfg file right now:


```cfg
[metadata]
name = mynodepack
version = 1.0.2
author = Kennedy Richard
author_email = kennedy@kennedyrichard.com
description = A template/example of how to package node packs
keywords = nodes, node, pack, example, template, nodezator
long_description = file: README.md
long_description_content_type = text/markdown
license = Unlicense
url = https://github.com/KennedyRichard/mynodepack
project_urls =
    Source = https://github.com/KennedyRichard/mynodepack
    Bug Tracker = https://github.com/KennedyRichard/mynodepack/issues
classifiers =
    License :: OSI Approved :: The Unlicense (Unlicense)
    Programming Language :: Python :: Implementation :: CPython

[options]
packages = find:
python_requires = >=3.7
install_requires =
    pygame-ce
```

Let's go over each configuration in the file contents above and additional possibilities.

| Setting | Data to edit |
|---------|--------------|
| `name` | put the name of you node pack here (the name of the node pack folder) |
| `version` | use whichever version you think represents the state of your code I recommend using [this convention](https://semver.org/)|
| `author` | your name |
| `author_email` | your email |
| `description` | a succinct description of your package |
| `long_description` | I recommend not changing this, just leave the README.md file as the long description |
| `long_description_content_type` | if you use the README.md, don't change this |
| `license` | the name of your license |
| `url` | the link to your project; if you have a dedicated website or blog, put it here, otherwise just use the github link |
| `project_urls` | you can list several kinds of urls here, you'll probably at least include the "Source" link |
| `classifiers` | use classifiers that apply to your project, the full list can be found [here](https://pypi.org/classifiers/) |
| `packages` | `find:` means the packaging software will discover the modules for you; leave this as-is |
| `python_requires` | replace the python version by the minimum Python version your code supports |
| `install_requires` | if any node imports an third-party module, you must list it here (or delete this configuration if you don't use any); we use [pygame-ce](https://pyga.me) in nodes from our `geometry_drawings` category, so we list it here |


Finally the **README.md** must contain info about your node pack. Here in this README.md file, instead of just presenting the nodes in this example node pack, most of the content is about explaining how to setup your own node pack, upload and install it, since it is the purpose of this repository. However, after you copy this file into your own package folder, you should delete its contents and only talk about the nodes in your node pack.

I actually added a section at the end of this README.md file, entitled `mynodepack`, which demonstrates how I would present the nodes in this repository. If you want, you can use it as a template as well.


### Add `__init__.py` files inside your node pack to allow package discovery

The only additional change you must perform is inside your node pack folder. You must add a `__init__.py` file...

- inside the node pack folder;
- inside all category folders;
- inside all node script folders;
- if any node script folder has other folder inside them (local subpackages), you must add a `__init__.py` file inside each folder that has at least one .py file used as a module.

This is needed in order for the packaging software to find all the packages/subpackages inside your node pack.

If you want, you can add all those files automatically by executing the following python snippet inside your node pack folder (just copy the code in a script.py file and execute it inside your node pack folder):

```python
from pathlib import Path

FILENAME = '__init__.py'

node_pack_dir = Path('.')

(node_pack_dir / FILENAME).touch() # __init__.py on node pack

category_folders = tuple(
    path
    for path in node_pack_dir.iterdir()
    if path.is_dir()
    if not path.name.startswith('.')
    if not path.name == '__pycache__'
)

for category_folder in category_folders:

    (category_folder / FILENAME).touch() # __init__.py on category

    node_script_folders = tuple(
        path
        for path in category_folder.iterdir()
        if path.is_dir()
        if not path.name.startswith('.')
        if not path.name == '__pycache__'
    )

    for node_script_folder in node_script_folders:

        (node_script_folder / FILENAME).touch() # __init__.py on script folders

        for subpath in node_script_folder.rglob('*'):
            if subpath.is_dir():
                if any(subpath.glob('*.py')):
                    (subpath / FILENAME).touch() # __init__.py on subfolders
```


### Generate distribution archives

First install the module needed to do that:

On Unix/macOS: `python3 -m pip install --upgrade build`

On Windows: `py -m pip install --upgrade build`

Now run this command from your package folder (the same directory where **pyproject.toml** is located):

On Unix/macOS: `python3 -m build`

On Windows: `py -m build`

After outputting a lot of text, a new `dist` folder should appear, containing a .whl file and a .tar.gz file. This are the distribution archives that we'll soon be uploading once you register an online account to upload your package.


### Register an account on PyPI or TestPyPI

Now that the distribution archives are ready, before even thinking about uploading it, you'll need to register an account on either [PyPI](https://pypi.org) or [TestPyPI](https://test.pypi.org). Both are instances of the Python Packaging Index. You can upload and install Python packages from both PyPI and TestPyPI. The only difference, as the name implies, is that TestPyPI is meant for testing/experimentation.

If you never uploaded a Python package before, I recommend TestPyPI, then, after uploading your package and ensuring it works properly, you can then upload it to PyPI.

After you register an account on one of those services, go to your account settings an on the **API tokens** section, generate a token with "All projects" as the scope and **do not close the page until you copy and save this token, since you won't be able to see it again**.


### Upload the distribution archives

Once registered and with the distribution archives ready for download, you must install the `twine` module to perform the upload:

On Unix/macOS: `python3 -m pip install --upgrade twine`

On Windows: `py -m pip install --upgrade twine`

Now run this command from your package folder (the same directory where the `dist` folder is located), which will upload the distribution files:

On Unix/macOS: `python3 -m twine upload --repository testpypi dist/*`

On Windows: `py -m twine upload --repository testpypi dist/*`

You must ommit the `--repository testpypi` part if you want to upload to PyPI instead of TestPyPI.

You'll then be prompted for you username and password. Type `__token__` as your username and press the `<Enter>` key, then, paste your token when prompted for your password and press `<Enter>` again.

After the command finishes executing, the output should be similar to this:

```
Uploading distributions to https://test.pypi.org/legacy/
Enter your username: __token__
Uploading nameofyournodepack-1.0.0-py3-none-any.whl
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 509.7/509.7 kB • 00:03 • ?
Uploading nameofyournodepack-1.0.0.tar.gz
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 422.5/422.5 kB • 00:02 • ?
```

From now on, every time you want upload a new version of your package, you just need to update the `version` information in the **setup.cfg** file and use the `build` and `twine upload` commands again as previously described.


### Test your package by installing it and loading the nodes into Nodezator

If everything went well, now it is time to test your package.

We recommend you do so in a dedicated virtual environment, but it is not mandatory. You can follow [this link](https://packaging.python.org/en/latest/tutorials/installing-packages/#creating-and-using-virtual-environments) to know more about setting up virtual environments.

To install your package, use this command:

On Unix/macOS: `python3 -m pip install --index-url https://test.pypi.org/simple/ nameofyournodepack`

On Windows: `py -m pip install --index-url https://test.pypi.org/simple/ nameofyournodepack`

You must ommit the `--index-url https://test.pypi.org/simple/` part if you uploaded your package to PyPI instead of TestPyPI.

After when executing the command, your package will be downloaded and installed. Its dependencies will also be loaded and installed (if they are not present already). At the end of the output, a message like this will be shown:

`Successfully installed nameofyournodepack-1.0.0`

Now open nodezator, create a new file and add your node pack as an installed one in the form you can access via the menubar command **<Graph > Select node packs>**. The process is explained [here](https://manual.nodezator.com/ch-distributing-nodes.html).

Now, to ensure you nodes were loaded correctly, just instantiate and play with them, try executing them, etc.


## mynodepack


### Installation

Install this node pack with `pip install mynodepack`. Then you'll be able to use it within the [nodezator app](https://github.com/IndiePython/nodezator) by adding `mynodepack` as an installed node pack, as explaned in the [manual](https://manual.nodezator.com/ch-loading-nodes.html).

### Purpose

The nodes in this node pack are dummy nodes used only to provide a demonstration of how to setup and upload a node pack to PyPI or TestPyPI. Even so, effort was taken to create meaningful nodes, that is, their operations are actually useful/make sense.


### Categories and its nodes

Below we provide a brief summary of each category and the nodes it contains.


| Category | Description |
|----------|-------------|
| `formula` | In mathematics, a formula is a fact, rule, or principle that is expressed in terms of mathematical symbols. Nodes in this category return specific values calculated using known mathematical formulas |
| `geometry_drawings` | Nodes from this category use [pygame-ce](https://pyga.me) to create surfaces with shapes drawn on them or modify/visualize/save such surfaces |


| Node | Description |
|------|-------------|
| `formula.hypotenuse` | This node uses the Pythagorean theorem formula to calculate the hypotenuse of a right-angled triangle given its legs |
| `formula.circle_area` | This node returns the area of a circle given its radius |


| Node | Description |
|------|-------------|
| `geometry_drawing.get_circle` | returns a surface with a circle drawn on it; you can specify color and radius |
| `geometry_drawing.get_rectangle` | returns a surface with a rectangle drawn on it; you can specify color, width and height |
| `geometry_drawing.combine_surfaces` | create a new surface by combining the given ones |
| `geometry_drawing.view_surface` | view a given surface |
| `geometry_drawing.save_surface` | saves a given surface on disk |
