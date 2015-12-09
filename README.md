# webrtcsrc
webrtcsrc

Prerequisite software

Depot Tools

You need to have the depot tools installed: See http://dev.chromium.org/developers/how-tos/install-depot-tools
Git: On Windows it will install a special version of Git automatically on the first sync. On Mac and Linux, you need to install Git by yourself (http://git-scm.com)
Linux (Ubuntu/Debian)

This, and more, is described on the Chromium site:
http://code.google.com/p/chromium/wiki/LinuxBuildInstructions

A script is provided for Ubuntu, which is available after your first gclient sync:
./build/install-build-deps.sh

Pulse Audio is missing from the script. On Ubuntu, this is provided by the libpulse-dev package.
Although the install-build-deps.sh script is the recommended method, it will install much more than you need. Here is a (hopefully complete) minimal list of packages to install (sudo apt-get install...):

g++ (>= 4.2)
python (>= 2.4)
libnss3-dev >= 3.12
libasound2-dev
libpulse-dev
libjpeg62-dev
libxv-dev
libgtk2.0-dev
libexpat1-dev

To create 32-bit builds for Linux on a 64-bit system (not needed or Android builds):
lib32asound2-dev
lib32z1
lib32ncurses5
lib32bz2-1.0

Tips for other distributions are available on the Chromium page.
Windows

Follow Chromium's build instructions at: http://www.chromium.org/developers/how-tos/build-instructions-windows.
OS X

XCode 3.0 or higher

Android

These instructions are tested on a Linux development machine. In WebRTC, we're using the same Android toolchain as Chrome (the one that is downloaded into third_party/android_tools). So you don't need to install NDK/SDK separately.
Install Chrome's Linux prerequisites
Install depot_tools
Install Java OpenJDK and the build dependencies as described at Android prerequisites





To Begin:
http://www.webrtc.org/native-code/development

Development

Contents

1 Before you start
2 Getting the code
3 Updating the code
4 Building
4.1 With Ninja
4.2 Using another build system
5 Working with Release Branches
6 Contributing Patches
7 Committing Code
7.1 Chromium committers
8 Example Applications
8.1 Peerconnection 
8.1.1 Setting up P2P calls between peerconnection_clients
8.1.2 Testing peerconnection_server
8.2 Call app
8.3 Relay server
8.4 Stun server
8.5 Turn server

The currently supported platforms are Windows, Mac OS X, Linux, Android and iOS. 
See the Android and iOS pages for build instructions and example applications specific to these mobile platforms.
Before you start

First, be sure to install the prerequisite software.
Getting the code

For desktop development:
If you're on Linux and have OpenJDK 7 installed in another location than Ubuntu's default:
export JAVA_HOME=<location of OpenJDK 7>
On Windows: launch a command prompt as Administrator.
Create a working directory, enter it, and run fetch webrtc:
mkdir webrtc-checkout
cd webrtc-checkout
fetch --nohooks webrtc
gclient sync
This will take a long time because it downloads the whole Chromium repository and dependencies which are several gigabytes. Do not interrupt this step or you may need to start all over agan (a new gclient sync may be enough but you might also need to start over cleanly).
Optionally you can specify how new branches should be tracked:
git config branch.autosetupmerge always
git config branch.autosetuprebase always
Alternatively, you can create new local branches like this (recommended):
cd src
git checkout master
git new-branch your-branch-name
See Android and iOS pages for separate instructions.
Updating the code

Update your current branch with:
git pull
NOTICE: if you're not on a branch, git pull won't work, and you'll need to use git fetch instead.

Peridically, the build toolchain and dependencies of WebRTC are updated. To get such updates, you must run:
gclient sync
In addition to downloading dependencies this also generates native build files for your environment using GYP during the execution of the hooks in the DEPS file. These hooks can also be run separately using gclient runhooks). 

Ninja is the default build system for all platforms. It is possible to just generate new build files by calling:
python webrtc/build/gyp_webrtc
This also runs as a part of the gclient runhooks step.
Building

Binaries are by default (i.e. when building with ninja) generated in out/Debug and out/Release for debug and release builds respectively. See Android and iOS for build instructions specific to those platforms.
With Ninja

Ninja project files are normally generated during the gclient sync/runhooks step. If you need to re-generate only the ninja files (like if you've wiped your out folder), run 
python webrtc/build/gyp_webrtc

Then compile with (standing in src/):
Debug:
ninja -C out/Debug
Release:
ninja -C out/Release
Using another build system

Other build systems are not fully supported (and may fail), such as Visual Studio on Win or Xcode on OSX. GYP supports a hybrid approach of using ninja for building, but VS/Xcode for editing and driving compilation. Set the GYP_GENERATORS environment variable to the string:

ninja,msvs-ninja for Visual Studio project building with ninja
ninja,xcode-ninja for Xcode
Note, when the build environment is set to generate Visual Studio project files, GYP will by default, generate a project for the latest version of Visual Studio installed on your computer. It is possible to specify the desired Visual Studio version as described below:
Set environment variable GYP_MSVS_VERSION=<version> before runhooks or manually run the following gyp command from the src/ directory (this replaces gclient runhooks):
python webrtc/build/gyp_webrtc -G msvs_version=<version>
Where <version> is on the form YYYY. And Chromium requests VS2013 for now.
Then use Visual Studio to open and build the src/all.sln solution file
Please refer www.chromium.org for more details.
Working with Release Branches

To see available release branches, run:
git branch -r 

NOTICE: If you only see your local branches, you have a checkout created before our switch to Git (March 24, 2015). In that case, first run:
cd /path/to/webrtc/src
gclient sync --with_branch_heads
git fetch origin
You should now have an entry like this under [remote "origin"] in .git/config:
fetch = +refs/branch-heads/*:refs/remotes/branch-heads/*

To create a local branch tracking a remote release branch (in this example, the 43 branch):
git checkout -b my_branch refs/remotes/branch-heads/43

Commit log for the branch: https://chromium.googlesource.com/external/webrtc/+log/branch-heads/43
To browse it: https://chromium.googlesource.com/external/webrtc/+/branch-heads/43

For more details, read Chromium's Working with Branches and Working with Release Branches pages.
Contributing Patches

Please see Contributing bug fixes for information on how to get your changes included in the WebRTC codebase. You'll also need to setup authentication for committing below.
Committing Code

To commit code you have to be a committer.
From March 24, 2015, the source of truth is the Git repository at https://chromium.googlesource.com/external/webrtc. To be able to push commits to it, you need to perform the steps below.

If you already have a .netrc/.gitcookies file (most Chromium committers already have), you can skip step 1-2.
Go to https://chromium.googlesource.com/new-password and login with your webrtc.org account. 
Follow the instructions on how to store the credentials in the .gitcookies file in your home directory.
Go to  https://chromium-review.googlesource.com and login with your webrtc.org account. This will create the user in the Gerrit permission system so it can be added to the right committers group.
Make sure you have set the user.name and user.email Git config settings as specified at the depot tools setup page. If you're also a Chromium committer: also read the next section.
Commit a change list to the Git repo using:
git cl land
NOTICE: On Windows, you'll need to run this in a Git bash shell in order for gclient to find the .gitcookies file.

Sometimes it's necessary to bypass the presubmit checks (like when fixing an error that has closed the tree). 
Then use the --bypass-hooks flag.
Chromium committers

Many WebRTC committers are also Chromium committers. To make sure to use the right account for pushing commits to WebRTC, use the user.email Git config setting. The recommended way is to have the chromium.org account set globally as described at the depot tools setup page and then set user.email locally for the WebRTC repos using (change to your webrtc.org address):
cd /path/to/webrtc/src
git config user.email yourname@webrtc.org 
Example Applications

WebRTC contains several example applications which can be found under src/webrtc/examples and src/talk/examples. Higher level applications are listed first.
Peerconnection 

Peerconnection consist of two applications using the WebRTC Native APIs: 
A server application, with target name peerconnection_server
A client application, with target name peerconnection_client (not currently supported on Mac/Android)
The client application has simple voice and video capabilities. The server enables client applications to initiate a call between clients by managing signaling messages generated by the clients.

Setting up P2P calls between peerconnection_clients

Start peerconnection_server. You should see the following message indicating that it is running:
Server listening on port 8888

Start any number of peerconnection_clients and connect them to the server. The client UI consists of a few parts:
Connecting to a server: when the application is started you must specify which machine (IP-address) the server application is running on. Once that is done you can press "Connect" or the return button.
Select a peer: once successfully connected to a server you can connect to a peer by double clicking or select+press return on a peer's name.
Video chat: when a peer has been successfully connected to, a Video chat will be displayed in full window.
Ending chat session: press Esc. You will now be back to selecting a peer.
Ending connection: press Esc and you will now be able to select  which server to connect to.
Testing peerconnection_server

Start an instance of peerconnection_server application.
Open src/talk/examples/peerconnection/server/server_test.html in your browser. Click connect. Observe that the peerconnection_server announces your connection. Open one more tab using the same page. Connect it too (with a different name). It is now possible to exchange messages between the connected peers.

Call app

Target name call (currently disabled). An application that establishes a call using libjingle. Call uses xmpp (as opposed to SDP used by WebRTC) to allow you to login using your gmail account and make audio/video calls with your gmail friends. It is built on top of libjingle to provide this functionality.
Further, you can specify input and output RTP dump for voice and video. It provides two samples of input RTP dump: voice.rtpdump which contains a stream of single channel, 16Khz voice encoded with G722, and video.rtpdump which contains a 320x240 video encoded with H264 AVC at 30 frames per second. The provided samples will inter-operate with Google Talk Video. If you use other input RTP dump, you may need to change the codecs in call_main.cc (lines 215 - 222).
Relay server

Target name relayserver. Relays traffic when a direct peer-to-peer connection can't be established. Can be used with the call application above.
Stun server

Target name stunserver. Implements the STUN protocol for Session Traversal Utilities for NAT as documented in rfc5389.
Turn server

Target name turnserver. In active development to reach compatibility with rfc5766.
