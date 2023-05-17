## Example SRSG training build environment set up

An example build environment for developing training content, currently based on 
the DiRAC version of the training infrastructure. Provided as a Vagrant/Ansible build,
and as instructions.

It's based on [Ed's instructions for setting this up](https://github.com/Southampton-RSG/rsg-meta/wiki/Training-Infrastructure-Build-System#command-line), 
but also adds a Vagrant box to host the  Jekyll server.

It also uses [rbenv](https://github.com/rbenv/rbenv) to install a supported Ruby
version, without overwriting any global Ruby installation.

### Vagrant/Ansible

#### Prerequisites

Tested on:

- Vagrant 2.3.4
- VirtualBox 6.1

### Instructions

Get hold of this repository:

```
$ git clone https://github.com/steve-crouch/srsg-training-build
```

Set up Python virtual environment, which is used to run Ansible to provision the VM:

```
$ cd srsg-training-build
$ python3 -m venv venv
$ source venv/bin/activate
$ python3 -m pip install -r requirements.txt 
```

Creating a config file from the template:

```
$ cp config_example.yml config.yml
```

Edit `config.yml` and set `training_repo_url` to the training material's GitHub
repository URL you want to edit, e.g.:

```
training_repo_url: https://github.com/Southampton-RSG-Training/dirac-intro-to-mpi
```

Provision the VM using Vagrant:

```
$ vagrant up
```

Then you should be able to SSH into the Vagrant box using `vagrant ssh`, and find
the training materials repo in the `/home/vagrant` user directory. You should then
be able to build the materials by following the instructions in the "Building..." 
section at the bottom of this README.

Note that you will need to set your Git identity from within the Vagrant VM, e.g. 
from within the materials repo directory:

```
$ git config user.name "Jane Doe"
$ git config user.email janedoe@example.com
```

#### Prerequisites

Assumes Linux 22.04 LTS, but can be adapted for other linuxes, and also Mac (e.g. 
ensuring xcode is installed, plus using Homebrew to install any packages if 
necessary).


#### Global set up

Install os packages:

```
$ sudo apt-get update
$ sudo apt-get install python3.10-venv make gcc autoconf patch build-essential rustc libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libgmp-dev libncurses5-dev libffi-dev libgdbm6 libgdbm-dev libdb-dev uuid-dev
```

Install rbenv, and use it to build and install Ruby 3.0.6:

```
$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
Add to end of ~/.bashrc: eval "$(${HOME}/.rbenv/bin/rbenv init - bash)"
Restart terminal
$ rbenv install 3.0.6
```

#### Set up for individual training material repositories

Get hold of the training materials repository, e.g.:

```
$ git clone https://github.com/Southampton-RSG-Training/dirac-intro-to-mpi
```

Set up Python virtual environment, which is used to bring in other training material
components (e.g. setup, blurbs) from other repositories.

NB: On Mac, you may need to install the `wheel` package using pip before installing 
the other prerequisites.

```
$ cd dirac-intro-to-mpi
$ python3 -m venv venv
$ source venv/bin/activate
$ python3 -m pip install -r requirements.txt 
```

Set up a version of Ruby to be used by Jekyll to serve the content:

```
$ rbenv local 3.0.6
$ gem install bundler
$ bundle install
```

To build and preview the content, see the next section.

#### Building materials and serving content locally

To build the training content locally and preview it in a browser:

```
$ cd dirac-intro-to-mpi
$ source venv/bin/activate
$ bash bin/build_me.sh
$ bundle exec jekyll serve --host=0.0.0.0 —incremental --watch --livereload
```

Then you can view materials in a browser at http://127.0.0.1:4000.
