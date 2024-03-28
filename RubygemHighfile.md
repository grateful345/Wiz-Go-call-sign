Secret api token for Trello input where neccessary: (b6ffa170da74846855071ebb9a12c2195e0a0373ef269a815e93da498ac2175c)
bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
Step 2: Install chruby and the latest Ruby with ruby-installPermalink

Install chruby and ruby-install with Homebrew:

brew install chruby ruby-install xz
Install the latest stable version of Ruby (supported by Jekyll):

ruby-install ruby 3.1.3
This will take a few minutes, and once it’s done, configure your shell to automatically use chruby:

echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.1.3" >> ~/.zshrc # run 'chruby' to see actual version
If you’re using Bash, replace .zshrc with .bash_profile. If you’re not sure, read this external guide to find out which shell you’re using.

Quit and relaunch Terminal, then check that everything is working:

ruby -v
It should show ruby 3.1.3p185 (2022-11-24 revision 1a6b16756e) or a newer version.

Next, read that same external guide for important notes about setting and switching between Ruby versions with chruby.

Install JekyllPermalink

After installing Ruby with chruby, install the latest Jekyll gem:

gem install jekyll
uname -m

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew doctor
If you get Your system is ready to brew, you can move on to Step 2. Otherwise, read what Homebrew is saying very carefully. They usually provide great instructions that you should follow.

On Apple Silicon Macs, Homebrew might tell you to run a few commands after the installation, such as:

echo "eval $(/opt/homebrew/bin/brew shellenv)" >> ~/.zprofile
eval $(/opt/homebrew/bin/brew shellenv)
Make sure to run those commands.

Quit and restart Terminal, then check if everything is working so far:

brew doctor
Step 2: Install chruby and the latest Ruby with ruby-install

Install chruby and ruby-install:

brew install chruby ruby-install
Then install the latest Ruby (currently 3.3.0):

ruby-install ruby

echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.3.0" >> ~/.zshrc
Note that 3.3.0 in the commands above assumes 3.3.0 is the version that was installed at the beginning of Step 2.

Quit and relaunch Terminal, then check that everything is working:

ruby -v
It should match the version you installed in step 2.

Step 3: Install a gem

Congrats! You now have a working Ruby development environment. You should now be able to install Rails, Jekyll, cocoapods, fastlane, bundler, compass, or whatever gem you’ve been trying to install for the past few days or months!

Just make sure you don’t use sudo to install any gems!

Now that you have a proper Ruby development environment, you can safely install gems with gem install followed by the name of the gem.

Note that some gems are not compatible with Apple Silicon (M1/M2). I recommend replacing them with more modern alternatives, or asking the maintainers if they’re willing to update them. Here are a couple of examples:

therubyracer (read How to Install (Or Get Rid Of) therubyracer on M1 or M2 Macs)
grpc (this fix should be coming soon)
Next, make sure to read the rest of this guide. It will save you a lot of confusion and headaches.

How to install different versions of Ruby and switch between them

To install an additional version, run ruby-install followed by the desired version. But first, you need to check which version of the Apple Command Line Tools (CLT) or Xcode you have:

brew config
Look for the lines at the bottom that start with CLT: and Xcode:. If either one of them starts with 14 or higher, and you’re trying to install a version older than 3.1.3, 3.0.5, or 2.7.7 (but not 2.6.x or older), then you’ll need to install Ruby like this:

ruby-install 3.1.2 -- --enable-shared
Otherwise, if you have CLT/Xcode version less than 14, install Ruby normally without any extra options:

ruby-install 3.1.2
If this sounds complicated, and you don’t want to remember which command to use depending on which Ruby version you want to install, or what kind of Mac you have, you can buy Ruby on Mac Ultimate. It automatically figures out the right settings to use, and all you have to do is run rom install ruby 2.7.8 for example. And if you’re trying to install Ruby 2.6.x on your Mac, or even older Ruby versions on Mac, Ruby on Mac Ultimate will likely be your best option.

To switch to this newly-installed version, quit and restart your terminal, then run chruby followed by the version. For example:

chruby 3.1.4
chruby is different from other Ruby version managers in that you can’t set a “global” Ruby version that’s automatically available everywhere. If you followed this guide, every time you open a new Terminal window or tab, chruby will switch to Ruby 3.3.0, or the version that appears last in your shell file. Then you should be able to install gems globally within that specific Ruby version with gem install name_of_gem.

However, if you then cd into a different directory that either doesn’t have a .ruby-version file, or if the .ruby-version file is set to a Ruby version that chruby doesn’t know about, chruby will revert to the system Ruby (the one that came with your Mac), which is 2.6.10 on the latest Monterey and higher (Ventura, Sonoma).

You definitely never want to be using the system Ruby! Here are 5 reasons why you shouldn’t use the system Ruby.

You can check by running which ruby. It if says /usr/bin/ruby, that’s the system Ruby, and you don’t want that. If you get errors while trying to install a gem, or running bundle install, the first thing you should check is that you’re using the right version of Ruby.

That’s why I recommend that all Ruby projects have a .ruby-version file that specifies one of the versions that chruby knows about. That way, when you cd into the project, chruby will automatically detect the .ruby-version file and switch to the version defined in the file.

Read the section below to learn how to set up and use the .ruby-version file.

Recommended way to automatically switch to the correct version

A highly-recommended way to automatically switch between versions is to add a .ruby-version file in your Ruby project with the desired version number, such as 3.1.4. To test that this works:

Create a test folder and cd into it:

cd ~
mkdir testing-chruby
cd testing-chruby
Create a file called .ruby-version with 3.1.4 in it:

echo '3.1.4' >> .ruby-version
This assumes that you already have 3.1.4 installed. If not, either install it, or replace 3.1.4 with a version you already have. You can check which versions are installed by running chruby.

cd into a folder outside of your project, such as your home folder: cd ~

Run ruby -v. It will probably say 2.6.10 (or older), which is the Ruby that came preinstalled on macOS.

cd - to go back to your project. The - is a shortcut to go back to the previous directory in the terminal.

Verify that ruby -v shows 3.1.4

Delete the test folder:

cd ~
rm -rf testing-chruby
For projects that have a Gemfile, it’s a good idea to specify the Ruby version in your Gemfile, and because it must match the one in the .ruby-version file, you can make that easy by telling the Gemfile to grab the version from the .ruby-version file, by putting this line in your Gemfile, right before the first gem:

ruby File.read(".ruby-version").strip
Here’s an example of what the first few lines of a Gemfile might look like:

source "https://rubygems.org"
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby File.read(".ruby-version").strip

gem "rails", "~> 7.0.4"
Important note about projects with older Ruby versions

If you’re trying to work with an existing Ruby project that has a Gemfile and/or a .ruby-version file that specified a version older than 3.2.3, 3.1.4, 3.0.6, or 2.7.8, you’ll need to either update your app to use a more recent version (best solution), or install the older version specified in your project (not recommended).

This is a very important concept to understand when working with Ruby, and many people waste time trying to install older versions of Ruby when the correct solution is to update the project instead.

For example, it’s not possible to install Ruby 2.7.0 or 2.7.1 on an Apple Silicon Mac (unless you use Rosetta, which I do not recommend). Instead, you should update your project to at least 2.7.8, and then to 3.1.4 because Ruby 2.7 reached end of life in March 2023. Follow the step-by-step instructions in my article that explains how and why to upgrade the Ruby version in your project.

Issues with existing projects

Now that you have a proper Ruby development environment, it won’t necessarily fix issues that are specific to your project. For example, if you’ve been getting errors after running bundle install in an old Ruby project, or when trying to run a Jekyll site with bundle exec jekyll serve, or a Rails app with bin/rails s, those errors could be because the gems and/or Ruby version in your project are outdated.

In the next two sections, I’ll go over how to tell if the issue is specific to your project, and troubleshooting tips.

How to tell if the issue is specific to your project

Check if you can create and run a brand new Rails app, and/or check if you can create and run a brand new Jekyll site.

If you’re able to generate and run a brand new Rails or Jekyll project using the instructions in the links above, then that means you have a working Ruby dev setup, and the issues you’re seeing in your existing project are specific to that project. Read the next section for tips on fixing the issues.

If you’re not able to generate and run a brand new Rails or Jekyll project, then that means something is wrong with your Ruby setup, and you’ll need to start over at the beginning of this tutorial, or buy Ruby on Mac.

How to fix issues in existing projects

The first thing I recommend is to make sure all your gems are up to date. Outdated gems are the most common source of errors.

For example, ffi is a common source of errors on Apple Silicon Macs (M1/M2). If you see ffi anywhere in the error message, it most likely means you’re using a version older than 1.15.2.

In that case, try updating ffi to the latest version:

bundle update ffi
Gem                    Current  Latest  Requested  Groups
i18n                   0.9.5    1.8.8
jekyll                 3.8.7    4.2.0   ~> 3.8.5   default
jekyll-sass-converter  1.5.2    2.1.0
kramdown               1.17.0   2.3.0
liquid                 4.0.3    5.0.0
mercenary              0.3.6    0.4.0
In their Gemfile, Jekyll was listed like this:

gem "jekyll", "~> 3.8.5"
You can also see this same version in the Requested column above.

So then they ran bundle update, which tries to update all outdated gems at the same time (see the end of this article for why you might not want to do this), and it said this about Jekyll:

Using jekyll 3.8.7
gem 'jekyll', '4.0.0.pre.beta1'
gem install jekyll --pre
gem 'mercenary', '~> 0.4.0'
gem install mercenary
gem 'bundler', '~> 2.5', '>= 2.5.7'

gem install bundler
gem 'em-websocket', '~> 0.5.3'

gem install em-websocket
gem 'eventmachine', '~> 1.2', '>= 1.2.7'
gem install eventmachine
gem 'rake-compiler', '~> 1.2', '>= 1.2.7'
gem install rake-compiler

gem install rake-compiler

$ gem update --system  # you might need to be an administrator or root
ruby setup.rb --help

variable setting
nil: respect environment variables (HTTP_PROXY, HTTP_PROXY_USER,
HTTP_PROXY_PASS)
:no_proxy: ignore environment variables and _don't_ use a proxy
dns: An object to use for DNS resolution of the API endpoint.
By default, use Resolv::DNS.
headers: A set of additional HTTP headers to be sent to the server when
fetching the gem.
Public Instance Methods

api_endpoint(uri)
Given a source at uri, calculate what hostname to actually connect to query the data for it.
# File lib/rubygems/remote_fetcher.rb, line 94
def api_endpoint(uri)
  host = uri.host

  begin
    res = @dns.getresource "_rubygems._tcp.#{host}",
                           Resolv::DNS::Resource::IN::SRV
  rescue Resolv::ResolvError => e
    verbose "Getting SRV record failed: #{e}"
    uri
  else
    target = res.target.to_s.strip

    if /\.#{Regexp.quote(host)}\z/ =~ target
      return URI.parse "#{uri.scheme}://#{target}#{uri.path}"
    end

    uri
  end
end
cache_update_path(uri, path 













gem install jekyll bundler
Create a new Jekyll site at ./myblog.
jekyll new myblog
Change into your new directory.
cd myblog
Build the site and make it available on a local server.
bundle exec jekyll serve
Browse to http://localhost:4000


ratchet/
├── css/
│   ├── ratchet.css
│   ├── ratchet.min.css
│   ├── ratchet-theme-android.css
│   ├── ratchet-theme-android.min.css
│   ├── ratchet-theme-ios.css
│   └── ratchet-theme-ios.min.css
├── js/
│   ├── ratchet.js
│   └── ratchet.min.js
└── fonts/
    ├── ratchicons.eot
    ├── ratchicons.svg
    ├── ratchicons.ttf
    └── ratchicons.woff
Copyright 2017 Google Inc. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS-IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
"""Module setuptools script."""

import distutils.command.build
from setuptools import setup

description = """PySC2 - StarCraft II Learning Environment

PySC2 is DeepMind's Python component of the StarCraft II Learning Environment
(SC2LE). It exposes Blizzard Entertainment's StarCraft II Machine Learning API
as a Python RL Environment. This is a collaboration between DeepMind and
Blizzard to develop StarCraft II into a rich environment for RL research. PySC2
provides an interface for RL agents to interact with StarCraft 2, getting
observations and sending actions.

We have published an accompanying blogpost and paper
https://deepmind.com/blog/deepmind-and-blizzard-open-starcraft-ii-ai-research-environment/
which outlines our motivation for using StarCraft II for DeepRL research, and
some initial research results using the environment.

Read the README at https://github.com/deepmind/pysc2 for more information.
"""


class BuildCommand(distutils.command.build.build):

  def initialize_options(self):
    distutils.command.build.build.initialize_options(self)
    # To avoid conflicting with the Bazel BUILD file.
    self.build_base = '_build'


setup(
    name='PySC2',
    version='4.0.0',
    description='Starcraft II environment and library for training agents.',
    long_description=description,
    author='DeepMind',
    author_email='pysc2@deepmind.com',
    cmdclass={'build': BuildCommand},
    license='Apache License, Version 2.0',
    keywords='StarCraft AI',
    url='https://github.com/deepmind/pysc2',
    packages=[
        'pysc2',
        'pysc2.agents',
        'pysc2.bin',
        'pysc2.env',
        'pysc2.lib',
        'pysc2.maps',
        'pysc2.run_configs',
        'pysc2.tests',
    ],
    install_requires=[
        'absl-py>=0.1.0',
        'deepdiff',
        'dm_env',
        'enum34',
        'mock',
        'mpyq',
        'numpy>=1.10',
        'portpicker>=1.2.0',
        'protobuf>=2.6',
        'pygame',
        'requests',
        's2clientprotocol>=4.10.1.75800.0',
        's2protocol',
        'sk-video',
        'websocket-client',
    ],
    entry_points={
        'console_scripts': [
            'pysc2_agent = pysc2.bin.agent:entry_point',
            'pysc2_play = pysc2.bin.play:entry_point',
            'pysc2_replay_info = pysc2.bin.replay_info:entry_point',
        ],
    },
    classifiers=[
        'Development Status :: 4 - Beta',
        'Environment :: Console',
        'Intended Audience :: Science/Research',
        'License :: OSI Approved :: Apache Software License',
        'Operating System :: POSIX :: Linux',
        'Operating System :: Microsoft :: Windows',
        'Operating System :: MacOS :: MacOS X',
        'Programming Language :: Python :: 3.8',
        'Programming Language :: Python :: 3.9',
        'Programming Language :: Python :: 3.10',
        'Programming Language :: Python :: 3.11',
        'Topic :: Scientific/Engineering :: Artificial Intelligence',
    ],
)
First of all,
[get Bazel](https://docs.bazel.build/versions/main/install-ubuntu.html). Next,
you may need the Python development environment.

```shell
$ sudo apt update
$ sudo apt install python3 python3-dev python3-venv
```

Build all PySC2 targets (from the workspace root).

```shell
$ bazel build --cxxopt='-std=c++17' ...
```

Run some tests.

```shell
$ bazel test --cxxopt='-std=c++17' pysc2/lib/...
```

Beyond that, everything should be the same as running Python directly as
described in the readme, only rather than using the Python interpreter you use
Bazel to run. For instance...

```shell
$ python -m pysc2.bin.agent --map Simple64
```

becomes...

```shell
$ bazel run --cxxopt='-std=c++17' pysc2/bin:agent -- --map Simple64
```

You may wish to use a [.bazelrc file](https://docs.bazel.build/versions/main/guide.html#bazelrc-the-bazel-configuration-file) to avoid the need to repeatedly specify command-line options, for instance `--cxxopt='-std=c++17'`.
import numpy

from pysc2.agents import base_agent
from pysc2.lib import actions


class RandomAgent(base_agent.BaseAgent):
  """A random agent for starcraft."""

  def step(self, obs):
    super(RandomAgent, self).step(obs)
    function_id = numpy.random.choice(obs.observation.available_actions)
    args = [[numpy.random.randint(0, size) for size in arg.sizes]
            for arg in self.action_spec.functions[function_id].args]
    return actions.FunctionCall(function_id, args)
starcraft."""

from pysc2.agents import base_agent

from s2clientprotocol import sc2api_pb2 as sc_pb


class NoOpAgent(base_agent.BaseAgent):
  """A no-op agent for starcraft."""

  def step(self, obs):
    super(NoOpAgent, self).step(obs)
    return sc_pb.Action()

"""Scripted agents."""

import numpy

from pysc2.agents import base_agent
from pysc2.lib import actions
from pysc2.lib import features

_PLAYER_SELF = features.PlayerRelative.SELF
_PLAYER_NEUTRAL = features.PlayerRelative.NEUTRAL  # beacon/minerals
_PLAYER_ENEMY = features.PlayerRelative.ENEMY

FUNCTIONS = actions.FUNCTIONS
RAW_FUNCTIONS = actions.RAW_FUNCTIONS


def _xy_locs(mask):
  """Mask should be a set of bools from comparison with a feature layer."""
  y, x = mask.nonzero()
  return list(zip(x, y))


class MoveToBeacon(base_agent.BaseAgent):
  """An agent specifically for solving the MoveToBeacon map."""

  def step(self, obs):
    super(MoveToBeacon, self).step(obs)
    if FUNCTIONS.Move_screen.id in obs.observation.available_actions:
      player_relative = obs.observation.feature_screen.player_relative
      beacon = _xy_locs(player_relative == _PLAYER_NEUTRAL)
      if not beacon:
        return FUNCTIONS.no_op()
      beacon_center = numpy.mean(beacon, axis=0).round()
      return FUNCTIONS.Move_screen("now", beacon_center)
    else:
      return FUNCTIONS.select_army("select")


class CollectMineralShards(base_agent.BaseAgent):
  """An agent specifically for solving the CollectMineralShards map."""

  def step(self, obs):
    super(CollectMineralShards, self).step(obs)
    if FUNCTIONS.Move_screen.id in obs.observation.available_actions:
      player_relative = obs.observation.feature_screen.player_relative
      minerals = _xy_locs(player_relative == _PLAYER_NEUTRAL)
      if not minerals:
        return FUNCTIONS.no_op()
      marines = _xy_locs(player_relative == _PLAYER_SELF)
      marine_xy = numpy.mean(marines, axis=0).round()  # Average location.
      distances = numpy.linalg.norm(numpy.array(minerals) - marine_xy, axis=1)
      closest_mineral_xy = minerals[numpy.argmin(distances)]
      return FUNCTIONS.Move_screen("now", closest_mineral_xy)
    else:
      return FUNCTIONS.select_army("select")


class CollectMineralShardsFeatureUnits(base_agent.BaseAgent):
  """An agent for solving the CollectMineralShards map with feature units.

  Controls the two marines independently:
  - select marine
  - move to nearest mineral shard that wasn't the previous target
  - swap marine and repeat
  """

  def setup(self, obs_spec, action_spec):
    super(CollectMineralShardsFeatureUnits, self).setup(obs_spec, action_spec)
    if "feature_units" not in obs_spec:
      raise Exception("This agent requires the feature_units observation.")

  def reset(self):
    super(CollectMineralShardsFeatureUnits, self).reset()
    self._marine_selected = False
    self._previous_mineral_xy = [-1, -1]

  def step(self, obs):
    super(CollectMineralShardsFeatureUnits, self).step(obs)
    marines = [unit for unit in obs.observation.feature_units
               if unit.alliance == _PLAYER_SELF]
    if not marines:
      return FUNCTIONS.no_op()
    marine_unit = next((m for m in marines
                        if m.is_selected == self._marine_selected), marines[0])
    marine_xy = [marine_unit.x, marine_unit.y]

    if not marine_unit.is_selected:
      # Nothing selected or the wrong marine is selected.
      self._marine_selected = True
      return FUNCTIONS.select_point("select", marine_xy)

    if FUNCTIONS.Move_screen.id in obs.observation.available_actions:
      # Find and move to the nearest mineral.
      minerals = [[unit.x, unit.y] for unit in obs.observation.feature_units
                  if unit.alliance == _PLAYER_NEUTRAL]

      if self._previous_mineral_xy in minerals:
        # Don't go for the same mineral shard as other marine.
        minerals.remove(self._previous_mineral_xy)

      if minerals:
        # Find the closest.
        distances = numpy.linalg.norm(
            numpy.array(minerals) - numpy.array(marine_xy), axis=1)
        closest_mineral_xy = minerals[numpy.argmin(distances)]

        # Swap to the other marine.
        self._marine_selected = False
        self._previous_mineral_xy = closest_mineral_xy
        return FUNCTIONS.Move_screen("now", closest_mineral_xy)

    return FUNCTIONS.no_op()


class CollectMineralShardsRaw(base_agent.BaseAgent):
  """An agent for solving CollectMineralShards with raw units and actions.

  Controls the two marines independently:
  - move to nearest mineral shard that wasn't the previous target
  - swap marine and repeat
  """

  def setup(self, obs_spec, action_spec):
    super(CollectMineralShardsRaw, self).setup(obs_spec, action_spec)
    if "raw_units" not in obs_spec:
      raise Exception("This agent requires the raw_units observation.")

  def reset(self):
    super(CollectMineralShardsRaw, self).reset()
    self._last_marine = None
    self._previous_mineral_xy = [-1, -1]

  def step(self, obs):
    super(CollectMineralShardsRaw, self).step(obs)
    marines = [unit for unit in obs.observation.raw_units
               if unit.alliance == _PLAYER_SELF]
    if not marines:
      return RAW_FUNCTIONS.no_op()
    marine_unit = next((m for m in marines if m.tag != self._last_marine))
    marine_xy = [marine_unit.x, marine_unit.y]

    minerals = [[unit.x, unit.y] for unit in obs.observation.raw_units
                if unit.alliance == _PLAYER_NEUTRAL]

    if self._previous_mineral_xy in minerals:
      # Don't go for the same mineral shard as other marine.
      minerals.remove(self._previous_mineral_xy)

    if minerals:
      # Find the closest.
      distances = numpy.linalg.norm(
          numpy.array(minerals) - numpy.array(marine_xy), axis=1)
      closest_mineral_xy = minerals[numpy.argmin(distances)]

      self._last_marine = marine_unit.tag
      self._previous_mineral_xy = closest_mineral_xy
      return RAW_FUNCTIONS.Move_pt("now", marine_unit.tag, closest_mineral_xy)

    return RAW_FUNCTIONS.no_op()


class DefeatRoaches(base_agent.BaseAgent):
  """An agent specifically for solving the DefeatRoaches map."""

  def step(self, obs):
    super(DefeatRoaches, self).step(obs)
    if FUNCTIONS.Attack_screen.id in obs.observation.available_actions:
      player_relative = obs.observation.feature_screen.player_relative
      roaches = _xy_locs(player_relative == _PLAYER_ENEMY)
      if not roaches:
        return FUNCTIONS.no_op()

      # Find the roach with max y coord.
      target = roaches[numpy.argmax(numpy.array(roaches)[:, 1])]
      return FUNCTIONS.Attack_screen("now", target)

    if FUNCTIONS.select_army.id in obs.observation.available_actions:
      return FUNCTIONS.select_army("select")

    return FUNCTIONS.no_op()


class DefeatRoachesRaw(base_agent.BaseAgent):
  """An agent specifically for solving DefeatRoaches using raw actions."""

  def setup(self, obs_spec, action_spec):
    super(DefeatRoachesRaw, self).setup(obs_spec, action_spec)
    if "raw_units" not in obs_spec:
      raise Exception("This agent requires the raw_units observation.")

  def step(self, obs):
    super(DefeatRoachesRaw, self).step(obs)
    marines = [unit.tag for unit in obs.observation.raw_units
               if unit.alliance == _PLAYER_SELF]
    roaches = [unit for unit in obs.observation.raw_units
               if unit.alliance == _PLAYER_ENEMY]

    if marines and roaches:
      # Find the roach with max y coord.
      target = sorted(roaches, key=lambda r: r.y)[0].tag
      return RAW_FUNCTIONS.Attack_unit("now", marines, target)

    return FUNCTIONS.no_op()






Run brew doctor
Please note that these warnings are just used to help the Homebrew maintainers
with debugging if you file an issue. If everything you use Homebrew for is
working fine: please don't worry or file an issue; just ignore this. Thanks!

Warning: Some installed formulae are deprecated or disabled.
You should find replacements for the following formulae:
  openssl@1.1
  packer

Warning: You have an unnecessary local Cask tap.
This can cause problems installing up-to-date casks.
Please remove it by running:
  brew untap homebrew/cask

Warning: You have an unnecessary local Core tap!
This can cause problems installing up-to-date formulae.
Please remove it by running:
 brew untap homebrew/core
Error: Process completed with exit code 1.
Run brew config
HOMEBREW_VERSION: 4.2.9
ORIGIN: https://github.com/Homebrew/brew
HEAD: e5fefd73cd97cd36ae3af29551f529ae59b333d6
Last commit: 4 weeks ago
Core tap HEAD: 7987fb379ba90bbd265cb5b41db8086a5e233d39
Core tap last commit: 4 weeks ago
Core cask tap HEAD: 10b89b8ddeac7513b77c0d19afe6b759146b6d65
Core cask tap last commit: 4 weeks ago
HOMEBREW_PREFIX: /usr/local
HOMEBREW_CASK_OPTS: ["--no-quarantine"]
HOMEBREW_COLOR: set
HOMEBREW_MAKE_JOBS: 4
HOMEBREW_NO_AUTO_UPDATE: set
HOMEBREW_NO_INSTALL_CLEANUP: set
Homebrew Ruby: 3.1.4 => /usr/local/Homebrew/Library/Homebrew/vendor/portable-ruby/3.1.4/bin/ruby
CPU: quad-core 64-bit ivybridge
Clang: 15.0.0 build 1500
Git: 2.43.2 => /usr/local/bin/git
Curl: 8.4.0 => /usr/bin/curl
macOS: 13.6.4-x86_64
CLT: 15.1.0.0.1.1700200546
Xcode: 15.0.1 => /Applications/Xcode_15.0.1.app/Contents/Developer
on: [push, pull_request]
name: Test on macOS
jobs:
  test:
    strategy:
      matrix:
        os: [macos-13]
    runs-on: ${{ matrix.os }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Install ImageMagick
        run: env HOMEBREW_NO_AUTO_UPDATE=1 brew install imagemagick
on: [push, pull_request]
name: Test on macOS
jobs:
  test:
    strategy:
      matrix:
        os: [macos-13]
    runs-on: ${{ matrix.os }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Install ImageMagick
        run: env HOMEBREW_NO_AUTO_UPDATE=1 brew install imagemagick
	
 $ pip install --upgrade https://github.com/deepmind/pysc2/archive/master.zip
$ git clone https://github.com/deepmind/pysc2.git
$ pip install --upgrade pysc2/
$ python -m pysc2.bin.agent --map Simple64
It runs a random agent by default, but you can specify others if you'd like, including your own.

$ python -m pysc2.bin.agent --map CollectMineralShards --agent pysc2.agents.scripted_agent.CollectMineralShards
You can also run two agents against each other.

$ python -m pysc2.bin.agent --map Simple64 --agent2 pysc2.agents.random_agent.RandomAgent
To specify the agent's race, the opponent's difficulty, and more, you can pass additional flags. Run with --help to see what you can change.

Play the game as a human

There is a human agent interface which is mainly used for debugging, but it can also be used to play the game. The UI is fairly simple and incomplete, but it's enough to understand the basics of the game. Also, it runs on Linux.

$ python -m pysc2.bin.play --map Simple64
In the UI, hit ? for a list of the hotkeys. The most basic ones are: F4 to quit, F5 to restart, F8 to save a replay, and Pgup/Pgdn to control the speed of the game. Otherwise use the mouse for selection and keyboard for commands listed on the left.

The left side is a basic rendering. The right side is the feature layers that the agent receives, with some coloring to make it more useful to us. You can enable or disable RGB or feature layer rendering and their resolutions with command-line flags.

Watch a replay

Running an agent and playing as a human save a replay by default. You can watch that replay by running:

$ python -m pysc2.bin.play --replay <path-to-replay>
This works for any replay as long as the map can be found by the game.

The same controls work as for playing the game, so F4 to exit, pgup/pgdn to control the speed, etc.

You can save a video of the replay with the --video flag.

List the maps

Maps need to be configured before they're known to the environment. You can see the list of known maps by running:

$ python -m pysc2.bin.map_list
Run the tests

If you want to submit a pull request, please make sure the tests pass on both python 2 and 3.

$ python -m pysc2.bin.run_tests
Environment Details

For a full description of the specifics of how the environment is configured, the observations and action spaces work read the environment documentation.

Note that an alternative to this environment is now available which provides an enriched action and observation format using the C++ wrappers developed for AlphaStar. See the converter documentation for more information.

Mini-game maps

The mini-game map files referenced in the paper are stored under pysc2/maps/ but must be installed in $SC2PATH/Maps

Location: /home/linuxbrew
Note: Homebrew is pre-installed on image but not added to PATH.
run the eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)" command
to accomplish this.

Both Xdebug and PCOV extensions are installed, but only Xdebug is enabled.

User: postgres
PostgreSQL service is disabled by default.
Use the following command as a part of your job to start the service: 'sudo systemctl start postgresql.service'
MySQL

MySQL 8.0.36-0ubuntu0.22.04.1
User: root
Password: root
MySQL service is disabled by default.
Use the following command as a part of your job to start the service: 'sudo systemctl start mysql.service'
MS SQL

sqlcmd 17.10.0001.1
SqlPackage 162.2.111.2
Cached Tools

Go

1.20.14
1.21.8
1.22.1
Node.js

16.20.2
18.19.1
20.11.1
Python

3.7.17
3.8.18
3.9.19
3.10.14
3.11.8
3.12.2
PyPy

3.7.13 [PyPy 7.3.9]
3.8.16 [PyPy 7.3.11]
3.9.18 [PyPy 7.3.15]
3.10.13 [PyPy 7.3.15]
Ruby

3.1.4
PowerShell Tools

PowerShell 7.4.1
PowerShell Modules

Az: 11.3.1
MarkdownPS: 1.9
Microsoft.Graph: 2.16.0
Pester: 5.5.0
PSScriptAnalyzer: 1.22.0
Web Servers

Name Version ConfigFile ServiceStatus ListenPort
apache2 2.4.52 /etc/apache2/apache2.conf inactive 80
nginx 1.18.0 /etc/nginx/nginx.conf inactive 80
Android

Package Name Version
Android Command Line Tools 9.0
Android SDK Build-tools 34.0.0
33.0.0 33.0.1 33.0.2 33.0.3
32.0.0
31.0.0
Android SDK Platforms android-34-ext8 (rev 1)
android-34-ext10 (rev 1)
android-34 (rev 3)
android-33-ext5 (rev 1)
android-33-ext4 (rev 1)
android-33 (rev 3)
android-32 (rev 1)
android-31 (rev 1)
Android Support Repository 47.0.0
CMake 3.10.2
3.18.1
3.22.1
Google Play services 49
Google Repository 58
NDK 24.0.8215888
25.2.9519653 (default)
26.2.11394342
Environment variables

Name Value
ANDROID_HOME /usr/local/lib/android/sdk
ANDROID_NDK /usr/local/lib/android/sdk/ndk/25.2.9519653
ANDROID_NDK_HOME /usr/local/lib/android/sdk/ndk/25.2.9519653
ANDROID_NDK_LATEST_HOME /usr/local/lib/android/sdk/ndk/26.2.11394342
ANDROID_NDK_ROOT /usr/local/lib/android/sdk/ndk/25.2.9519653
ANDROID_SDK_ROOT /usr/local/lib/android/sdk
Cached Docker images

Repository:Tag Digest Created
alpine:3.16 sha256:452e7292acee0ee16c332324d7de05fa2c99f9994ecc9f0779c602916a672ae4 2024-01-27
alpine:3.17 sha256:53cf9478b76f4c8fae126acbdfb79bed6e69e628faff572ebe4a029d3d247d98 2024-01-27
alpine:3.18 sha256:11e21d7b981a59554b3f822c49f6e9f57b6068bb74f49c4cd5cc4c663c7e5160 2024-01-27
debian:10 sha256:f6b3b7c7b049c2c7d0f19ae988b4eac64fd8e127fa891c9de1d3cf3f8c33cad4 2024-03-12
debian:11 sha256:5a87974e73c64b3fb161d444a84bdd47c0e6b6058eacaeea64342e7cbce1f04d 2024-03-12
moby/buildkit:latest sha256:00d2c6b8f39ae515e0eadd74f39e71a5efdc94321c9b919692a2aa32deef2bb1 2024-03-18
node:16 sha256:f77a1aef2da8d83e45ec990f45df50f1a286c5fe8bbfb8c6e4246c6389705c0b 2023-09-07
node:16-alpine sha256:a1f9d027912b58a7c75be7716c97cfbc6d3099f3a97ed84aa490be9dee20e787 2023-08-10
node:18 sha256:b39895225fb1984139d5af76400aff8fac3dd5bc00dd41a3ce22fc8a6cf538d5 2024-03-12
node:18-alpine sha256:c7620fdecfefb96813da62519897808775230386f4c8482e972e37b8b18cb460 2024-03-16
node:20 sha256:e06aae17c40c7a6b5296ca6f942a02e6737ae61bbbf3e2158624bb0f887991b5 2024-03-12
node:20-alpine sha256:bf77dc26e48ea95fca9d1aceb5acfa69d2e546b765ec2abfb502975f1a2d4def 2024-03-16
ubuntu:20.04 sha256:80ef4a44043dec4490506e6cc4289eeda2d106a70148b74b5ae91ee670e9c35d 2024-02-16
ubuntu:22.04 sha256:77906da86b60585ce12215807090eb327e7386c8fafb5402369e421f44eff17e 2024-02-27
Installed apt packages

Name Version
acl 2.3.1-1
aria2 1.36.0-1
autoconf 2.71-2
automake 1:1.16.5-1.3
binutils 2.38-4ubuntu2.6
bison 2:3.8.2+dfsg-1build1
brotli 1.0.9-2build6
bzip2 1.0.8-5build1
coreutils 8.32-4.1ubuntu1.1
curl 7.81.0-1ubuntu1.15
dbus 1.12.20-2ubuntu4.1
dnsutils 1:9.18.18-0ubuntu0.22.04.2
dpkg 1.21.1ubuntu2.3
dpkg-dev 1.21.1ubuntu2.3
fakeroot 1.28-1ubuntu1
file 1:5.41-3ubuntu0.1
findutils 4.8.0-1ubuntu3
flex 2.6.4-8build2
fonts-noto-color-emoji 2.042-0ubuntu0.22.04.1
ftp 20210827-4build1
g++ 4:11.2.0-1ubuntu1
gcc 4:11.2.0-1ubuntu1
gnupg2 2.2.27-3ubuntu2.1
haveged 1.9.14-1ubuntu1
imagemagick 8:6.9.11.60+dfsg-1.3ubuntu0.22.04.3
iproute2 5.15.0-1ubuntu2
iputils-ping 3:20211215-1
jq 1.6-2.1ubuntu3
lib32z1 1:1.2.11.dfsg-2ubuntu9.2
libc++-dev 1:14.0-55~exp2
libc++abi-dev 1:14.0-55~exp2
libc6-dev 2.35-0ubuntu3.6
libcurl4 7.81.0-1ubuntu1.15
libgbm-dev 23.2.1-1ubuntu3.1~22.04.2
libgconf-2-4 3.2.6-7ubuntu2
libgsl-dev 2.7.1+dfsg-3
libgtk-3-0 3.24.33-1ubuntu2
libmagic-dev 1:5.41-3ubuntu0.1
libmagickcore-dev 8:6.9.11.60+dfsg-1.3ubuntu0.22.04.3
libmagickwand-dev 8:6.9.11.60+dfsg-1.3ubuntu0.22.04.3
libsecret-1-dev 0.20.5-2
libsqlite3-dev 3.37.2-2ubuntu0.3
libssl-dev 3.0.2-0ubuntu1.15
libtool 2.4.6-15build2
libunwind8 1.3.2-2build2.1
libxkbfile-dev 1:1.1.0-1build3
libxss1 1:1.2.3-1build2
libyaml-dev 0.2.2-1build2
locales 2.35-0ubuntu3.6
lz4 1.9.3-2build2
m4 1.4.18-5ubuntu2
make 4.3-4.1build1
mediainfo 22.03-1
mercurial 6.1.1-1ubuntu1
net-tools 1.60+git20181103.0eebece-1ubuntu5
netcat 1.218-4ubuntu1
openssh-client 1:8.9p1-3ubuntu0.6
p7zip-full 16.02+dfsg-8
p7zip-rar 16.02-3build1
parallel 20210822+ds-2
pass 1.7.4-5
patchelf 0.14.3-1
pigz 2.6-1
pkg-config 0.29.2-1ubuntu3
pollinate 4.33-3ubuntu2
python-is-python3 3.9.2-2
rpm 4.17.0+dfsg1-4build1
rsync 3.2.7-0ubuntu0.22.04.2
shellcheck 0.8.0-2
sphinxsearch 2.2.11-8
sqlite3 3.37.2-2ubuntu0.3
ssh 1:8.9p1-3ubuntu0.6
sshpass 1.09-1
subversion 1.14.1-3ubuntu0.22.04.1
sudo 1.9.9-1ubuntu2.4
swig 4.0.2-1ubuntu1
tar 1.34+dfsg-1ubuntu0.1.22.04.2
telnet 0.17-44build1
texinfo 6.8-4build1
time 1.9-0.1build2
tk 8.6.11+1build2
tzdata 2024a-0ubuntu0.22.04
unzip 6.0-26ubuntu3.2
upx 3.96-3
wget 1.21.2-2ubuntu1
xorriso 1.5.4-2
xvfb 2:21.1.4-2ubuntu1.7~22.04.8
xz-utils 5.2.5-2ubuntu1
zip 3.0-12build2
zsync 0.6.2-3ubuntu1


$$$ Smack $$$

#include <linux/types.h>
#include <linux/slab.h>
#include <linux/fs.h>
#include <linux/sched.h>
#include "smack.h"

struct smack_known smack_known_huh = {
	.smk_known	= "?",
	.smk_secid	= 2,
};

struct smack_known smack_known_hat = {
	.smk_known	= "^",
	.smk_secid	= 3,
};

struct smack_known smack_known_star = {
	.smk_known	= "*",
	.smk_secid	= 4,
};

struct smack_known smack_known_floor = {
	.smk_known	= "_",
	.smk_secid	= 5,
};

struct smack_known smack_known_web = {
	.smk_known	= "@",
	.smk_secid	= 7,
};

LIST_HEAD(smack_known_list);

/*
 * The initial value needs to be bigger than any of the
 * known values above.
 */
static u32 smack_next_secid = 10;

/*
 * what events do we log
 * can be overwritten at run-time by /smack/logging
 */
int log_policy = SMACK_AUDIT_DENIED;

/**
 * smk_access_entry - look up matching access rule
 * @subject_label: a pointer to the subject's Smack label
 * @object_label: a pointer to the object's Smack label
 * @rule_list: the list of rules to search
 *
 * This function looks up the subject/object pair in the
 * access rule list and returns the access mode. If no
 * entry is found returns -ENOENT.
 *
 * NOTE:
 *
 * Earlier versions of this function allowed for labels that
 * were not on the label list. This was done to allow for
 * labels to come over the network that had never been seen
 * before on this host. Unless the receiving socket has the
 * star label this will always result in a failure check. The
 * star labeled socket case is now handled in the networking
 * hooks so there is no case where the label is not on the
 * label list. Checking to see if the address of two labels
 * is the same is now a reliable test.
 *
 * Do the object check first because that is more
 * likely to differ.
 *
 * Allowing write access implies allowing locking.
 */
int smk_access_entry(char *subject_label, char *object_label,
			struct list_head *rule_list)
{
	int may = -ENOENT;
	struct smack_rule *srp;

	list_for_each_entry_rcu(srp, rule_list, list) {
		if (srp->smk_object->smk_known == object_label &&
		    srp->smk_subject->smk_known == subject_label) {
			may = srp->smk_access;
			break;
		}
	}

	/*
	 * MAY_WRITE implies MAY_LOCK.
	 */
	if ((may & MAY_WRITE) == MAY_WRITE)
		may |= MAY_LOCK;
	return may;
}

/**
 * smk_access - determine if a subject has a specific access to an object
 * @subject: a pointer to the subject's Smack label entry
 * @object: a pointer to the object's Smack label entry
 * @request: the access requested, in "MAY" format
 * @a : a pointer to the audit data
 *
 * This function looks up the subject/object pair in the
 * access rule list and returns 0 if the access is permitted,
 * non zero otherwise.
 *
 * Smack labels are shared on smack_list
 */
int smk_access(struct smack_known *subject, struct smack_known *object,
	       int request, struct smk_audit_info *a)
{
	int may = MAY_NOT;
	int rc = 0;

	/*
	 * Hardcoded comparisons.
	 */
	/*
	 * A star subject can't access any object.
	 */
	if (subject == &smack_known_star) {
		rc = -EACCES;
		goto out_audit;
	}
	/*
	 * An internet object can be accessed by any subject.
	 * Tasks cannot be assigned the internet label.
	 * An internet subject can access any object.
	 */
	if (object == &smack_known_web || subject == &smack_known_web)
		goto out_audit;
	/*
	 * A star object can be accessed by any subject.
	 */
	if (object == &smack_known_star)
		goto out_audit;
	/*
	 * An object can be accessed in any way by a subject
	 * with the same label.
	 */
	if (subject->smk_known == object->smk_known)
		goto out_audit;
	/*
	 * A hat subject can read or lock any object.
	 * A floor object can be read or locked by any subject.
	 */
	if ((request & MAY_ANYREAD) == request ||
	    (request & MAY_LOCK) == request) {
		if (object == &smack_known_floor)
			goto out_audit;
		if (subject == &smack_known_hat)
			goto out_audit;
	}
	/*
	 * Beyond here an explicit relationship is required.
	 * If the requested access is contained in the available
	 * access (e.g. read is included in readwrite) it's
	 * good. A negative response from smk_access_entry()
	 * indicates there is no entry for this pair.
	 */
	rcu_read_lock();
	may = smk_access_entry(subject->smk_known, object->smk_known,
			       &subject->smk_rules);
	rcu_read_unlock();

	if (may <= 0 || (request & may) != request) {
		rc = -EACCES;
		goto out_audit;
	}
#ifdef CONFIG_SECURITY_SMACK_BRINGUP
	/*
	 * Return a positive value if using bringup mode.
	 * This allows the hooks to identify checks that
	 * succeed because of "b" rules.
	 */
	if (may & MAY_BRINGUP)
		rc = SMACK_BRINGUP_ALLOW;
#endif

out_audit:

#ifdef CONFIG_SECURITY_SMACK_BRINGUP
	if (rc < 0) {
		if (object == smack_unconfined)
			rc = SMACK_UNCONFINED_OBJECT;
		if (subject == smack_unconfined)
			rc = SMACK_UNCONFINED_SUBJECT;
	}
#endif

#ifdef CONFIG_AUDIT
	if (a)
		smack_log(subject->smk_known, object->smk_known,
			  request, rc, a);
#endif

	return rc;
}

/**
 * smk_tskacc - determine if a task has a specific access to an object
 * @tsp: a pointer to the subject's task
 * @obj_known: a pointer to the object's label entry
 * @mode: the access requested, in "MAY" format
 * @a : common audit data
 *
 * This function checks the subject task's label/object label pair
 * in the access rule list and returns 0 if the access is permitted,
 * non zero otherwise. It allows that the task may have the capability
 * to override the rules.
 */
int smk_tskacc(struct task_smack *tsp, struct smack_known *obj_known,
	       u32 mode, struct smk_audit_info *a)
{
	struct smack_known *sbj_known = smk_of_task(tsp);
	int may;
	int rc;

	/*
	 * Check the global rule list
	 */
	rc = smk_access(sbj_known, obj_known, mode, NULL);
	if (rc >= 0) {
		/*
		 * If there is an entry in the task's rule list
		 * it can further restrict access.
		 */
		may = smk_access_entry(sbj_known->smk_known,
				       obj_known->smk_known,
				       &tsp->smk_rules);
		if (may < 0)
			goto out_audit;
		if ((mode & may) == mode)
			goto out_audit;
		rc = -EACCES;
	}

	/*
	 * Allow for priviliged to override policy.
	 */
	if (rc != 0 && smack_privileged(CAP_MAC_OVERRIDE))
		rc = 0;

out_audit:
#ifdef CONFIG_AUDIT
	if (a)
		smack_log(sbj_known->smk_known, obj_known->smk_known,
			  mode, rc, a);
#endif
	return rc;
}

/**
 * smk_curacc - determine if current has a specific access to an object
 * @obj_known: a pointer to the object's Smack label entry
 * @mode: the access requested, in "MAY" format
 * @a : common audit data
 *
 * This function checks the current subject label/object label pair
 * in the access rule list and returns 0 if the access is permitted,
 * non zero otherwise. It allows that current may have the capability
 * to override the rules.
 */
int smk_curacc(struct smack_known *obj_known,
	       u32 mode, struct smk_audit_info *a)
{
	struct task_smack *tsp = current_security();

	return smk_tskacc(tsp, obj_known, mode, a);
}

#ifdef CONFIG_AUDIT
/**
 * smack_str_from_perm : helper to transalate an int to a
 * readable string
 * @string : the string to fill
 * @access : the int
 *
 */
static inline void smack_str_from_perm(char *string, int access)
{
	int i = 0;

	if (access & MAY_READ)
		string[i++] = 'r';
	if (access & MAY_WRITE)
		string[i++] = 'w';
	if (access & MAY_EXEC)
		string[i++] = 'x';
	if (access & MAY_APPEND)
		string[i++] = 'a';
	if (access & MAY_TRANSMUTE)
		string[i++] = 't';
	if (access & MAY_LOCK)
		string[i++] = 'l';
	string[i] = '\0';
}
/**
 * smack_log_callback - SMACK specific information
 * will be called by generic audit code
 * @ab : the audit_buffer
 * @a  : audit_data
 *
 */
static void smack_log_callback(struct audit_buffer *ab, void *a)
{
	struct common_audit_data *ad = a;
	struct smack_audit_data *sad = ad->smack_audit_data;
	audit_log_format(ab, "lsm=SMACK fn=%s action=%s",
			 ad->smack_audit_data->function,
			 sad->result ? "denied" : "granted");
	audit_log_format(ab, " subject=");
	audit_log_untrustedstring(ab, sad->subject);
	audit_log_format(ab, " object=");
	audit_log_untrustedstring(ab, sad->object);
	if (sad->request[0] == '\0')
		audit_log_format(ab, " labels_differ");
	else
		audit_log_format(ab, " requested=%s", sad->request);
}

/**
 *  smack_log - Audit the granting or denial of permissions.
 *  @subject_label : smack label of the requester
 *  @object_label  : smack label of the object being accessed
 *  @request: requested permissions
 *  @result: result from smk_access
 *  @a:  auxiliary audit data
 *
 * Audit the granting or denial of permissions in accordance
 * with the policy.
 */
void smack_log(char *subject_label, char *object_label, int request,
	       int result, struct smk_audit_info *ad)
{
#ifdef CONFIG_SECURITY_SMACK_BRINGUP
	char request_buffer[SMK_NUM_ACCESS_TYPE + 5];
#else
	char request_buffer[SMK_NUM_ACCESS_TYPE + 1];
#endif
	struct smack_audit_data *sad;
	struct common_audit_data *a = &ad->a;

	/* check if we have to log the current event */
	if (result < 0 && (log_policy & SMACK_AUDIT_DENIED) == 0)
		return;
	if (result == 0 && (log_policy & SMACK_AUDIT_ACCEPT) == 0)
		return;

	sad = a->smack_audit_data;

	if (sad->function == NULL)
		sad->function = "unknown";

	/* end preparing the audit data */
	smack_str_from_perm(request_buffer, request);
	sad->subject = subject_label;
	sad->object  = object_label;
#ifdef CONFIG_SECURITY_SMACK_BRINGUP
	/*
	 * The result may be positive in bringup mode.
	 * A positive result is an allow, but not for normal reasons.
	 * Mark it as successful, but don't filter it out even if
	 * the logging policy says to do so.
	 */
	if (result == SMACK_UNCONFINED_SUBJECT)
		strcat(request_buffer, "(US)");
	else if (result == SMACK_UNCONFINED_OBJECT)
		strcat(request_buffer, "(UO)");

	if (result > 0)
		result = 0;
#endif
	sad->request = request_buffer;
	sad->result  = result;

	common_lsm_audit(a, smack_log_callback, NULL);
}
#else /* #ifdef CONFIG_AUDIT */
void smack_log(char *subject_label, char *object_label, int request,
               int result, struct smk_audit_info *ad)
{
}
#endif

DEFINE_MUTEX(smack_known_lock);

struct hlist_head smack_known_hash[SMACK_HASH_SLOTS];

/**
 * smk_insert_entry - insert a smack label into a hash map,
 *
 * this function must be called under smack_known_lock
 */
void smk_insert_entry(struct smack_known *skp)
{
	unsigned int hash;
	struct hlist_head *head;

	hash = full_name_hash(NULL, skp->smk_known, strlen(skp->smk_known));
	head = &smack_known_hash[hash & (SMACK_HASH_SLOTS - 1)];

	hlist_add_head_rcu(&skp->smk_hashed, head);
	list_add_rcu(&skp->list, &smack_known_list);
}

/**
 * smk_find_entry - find a label on the list, return the list entry
 * @string: a text string that might be a Smack label
 *
 * Returns a pointer to the entry in the label list that
 * matches the passed string or NULL if not found.
 */
struct smack_known *smk_find_entry(const char *string)
{
	unsigned int hash;
	struct hlist_head *head;
	struct smack_known *skp;

	hash = full_name_hash(NULL, string, strlen(string));
	head = &smack_known_hash[hash & (SMACK_HASH_SLOTS - 1)];

	hlist_for_each_entry_rcu(skp, head, smk_hashed)
		if (strcmp(skp->smk_known, string) == 0)
			return skp;

	return NULL;
}

/**
 * smk_parse_smack - parse smack label from a text string
 * @string: a text string that might contain a Smack label
 * @len: the maximum size, or zero if it is NULL terminated.
 *
 * Returns a pointer to the clean label or an error code.
 */
char *smk_parse_smack(const char *string, int len)
{
	char *smack;
	int i;

	if (len <= 0)
		len = strlen(string) + 1;

	/*
	 * Reserve a leading '-' as an indicator that
	 * this isn't a label, but an option to interfaces
	 * including /smack/cipso and /smack/cipso2
	 */
	if (string[0] == '-')
		return ERR_PTR(-EINVAL);

	for (i = 0; i < len; i++)
		if (string[i] > '~' || string[i] <= ' ' || string[i] == '/' ||
		    string[i] == '"' || string[i] == '\\' || string[i] == '\'')
			break;

	if (i == 0 || i >= SMK_LONGLABEL)
		return ERR_PTR(-EINVAL);

	smack = kzalloc(i + 1, GFP_KERNEL);
	if (smack == NULL)
		return ERR_PTR(-ENOMEM);

	strncpy(smack, string, i);

	return smack;
}

/**
 * smk_netlbl_mls - convert a catset to netlabel mls categories
 * @catset: the Smack categories
 * @sap: where to put the netlabel categories
 *
 * Allocates and fills attr.mls
 * Returns 0 on success, error code on failure.
 */
int smk_netlbl_mls(int level, char *catset, struct netlbl_lsm_secattr *sap,
			int len)
{
	unsigned char *cp;
	unsigned char m;
	int cat;
	int rc;
	int byte;

	sap->flags |= NETLBL_SECATTR_MLS_CAT;
	sap->attr.mls.lvl = level;
	sap->attr.mls.cat = NULL;

	for (cat = 1, cp = catset, byte = 0; byte < len; cp++, byte++)
		for (m = 0x80; m != 0; m >>= 1, cat++) {
			if ((m & *cp) == 0)
				continue;
			rc = netlbl_catmap_setbit(&sap->attr.mls.cat,
						  cat, GFP_KERNEL);
			if (rc < 0) {
				netlbl_catmap_free(sap->attr.mls.cat);
				return rc;
			}
		}

	return 0;
}

/**
 * smk_import_entry - import a label, return the list entry
 * @string: a text string that might be a Smack label
 * @len: the maximum size, or zero if it is NULL terminated.
 *
 * Returns a pointer to the entry in the label list that
 * matches the passed string, adding it if necessary,
 * or an error code.
 */
struct smack_known *smk_import_entry(const char *string, int len)
{
	struct smack_known *skp;
	char *smack;
	int slen;
	int rc;

	smack = smk_parse_smack(string, len);
	if (IS_ERR(smack))
		return ERR_CAST(smack);

	mutex_lock(&smack_known_lock);

	skp = smk_find_entry(smack);
	if (skp != NULL)
		goto freeout;

	skp = kzalloc(sizeof(*skp), GFP_KERNEL);
	if (skp == NULL) {
		skp = ERR_PTR(-ENOMEM);
		goto freeout;
	}

	skp->smk_known = smack;
	skp->smk_secid = smack_next_secid++;
	skp->smk_netlabel.domain = skp->smk_known;
	skp->smk_netlabel.flags =
		NETLBL_SECATTR_DOMAIN | NETLBL_SECATTR_MLS_LVL;
	/*
	 * If direct labeling works use it.
	 * Otherwise use mapped labeling.
	 */
	slen = strlen(smack);
	if (slen < SMK_CIPSOLEN)
		rc = smk_netlbl_mls(smack_cipso_direct, skp->smk_known,
			       &skp->smk_netlabel, slen);
	else
		rc = smk_netlbl_mls(smack_cipso_mapped, (char *)&skp->smk_secid,
			       &skp->smk_netlabel, sizeof(skp->smk_secid));

	if (rc >= 0) {
		INIT_LIST_HEAD(&skp->smk_rules);
		mutex_init(&skp->smk_rules_lock);
		/*
		 * Make sure that the entry is actually
		 * filled before putting it on the list.
		 */
		smk_insert_entry(skp);
		goto unlockout;
	}
	/*
	 * smk_netlbl_mls failed.
	 */
	kfree(skp);
	skp = ERR_PTR(rc);
freeout:
	kfree(smack);
unlockout:
	mutex_unlock(&smack_known_lock);

	return skp;
}

/**
 * smack_from_secid - find the Smack label associated with a secid
 * @secid: an integer that might be associated with a Smack label
 *
 * Returns a pointer to the appropriate Smack label entry if there is one,
 * otherwise a pointer to the invalid Smack label.
 */
struct smack_known *smack_from_secid(const u32 secid)
{
	struct smack_known *skp;

	rcu_read_lock();
	list_for_each_entry_rcu(skp, &smack_known_list, list) {
		if (skp->smk_secid == secid) {
			rcu_read_unlock();
			return skp;
		}
	}

	/*
	 * If we got this far someone asked for the translation
	 * of a secid that is not on the list.
	 */
	rcu_read_unlock();
	return &smack_known_huh;
}

/*
 * Unless a process is running with one of these labels
 * even having CAP_MAC_OVERRIDE isn't enough to grant
 * privilege to violate MAC policy. If no labels are
 * designated (the empty list case) capabilities apply to
 * everyone.
 */
LIST_HEAD(smack_onlycap_list);
DEFINE_MUTEX(smack_onlycap_lock);

/**
 * smack_privileged_cred - are all privilege requirements met by cred
 * @cap: The requested capability
 * @cred: the credential to use
 *
 * Is the task privileged and allowed to be privileged
 * by the onlycap rule.
 *
 * Returns true if the task is allowed to be privileged, false if it's not.
 */
bool smack_privileged_cred(int cap, const struct cred *cred)
{
	struct task_smack *tsp = cred->security;
	struct smack_known *skp = tsp->smk_task;
	struct smack_known_list_elem *sklep;
	int rc;

	rc = cap_capable(cred, &init_user_ns, cap, SECURITY_CAP_AUDIT);
	if (rc)
		return false;

	rcu_read_lock();
	if (list_empty(&smack_onlycap_list)) {
		rcu_read_unlock();
		return true;
	}

	list_for_each_entry_rcu(sklep, &smack_onlycap_list, list) {
		if (sklep->smk_label == skp) {
			rcu_read_unlock();
			return true;
		}
	}
	rcu_read_unlock();

	return false;
}

/**
 * smack_privileged - are all privilege requirements met
 * @cap: The requested capability
 *
 * Is the task privileged and allowed to be privileged
 * by the onlycap rule.
 *
 * Returns true if the task is allowed to be privileged, false if it's not.
 */
bool smack_privileged(int cap)
{
	/*
	 * All kernel tasks are privileged
	 */
	if (unlikely(current->flags & PF_KTHREAD))
		return true;

	return smack_privileged_cred(cap, current_cred());
}
