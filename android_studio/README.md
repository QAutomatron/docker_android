# Dockerfile for Android development with NDK and Android Studio
Docker image for desktop development using Android Studio, based on menny/android_ndk image.<br/>

Like this (artifacts only in the screenshot, not in real life):

![Screenshot of AnySoftKeyboard inside Docker](android_studio_ask_screenshot.png "Screenshot of AnySoftKeyboard inside Docker")

## Contains:

* All the good staff from the [General Android image](https://github.com/menny/docker_android/blob/master/README.md).
* All the good staff from the [NDK Android image](https://github.com/menny/docker_android/blob/master/android_ndk/README.md).
* Android Studio 3.0.0 RC1.
* API 26 Sources.
* X11 support.
* [Links2](http://links.twibright.com/) - A very basic web-browser.

## Accepting licenses
[Read](https://github.com/menny/docker_android/blob/master/README.md#accepting-licenses) about this at the general Android Docker image.

## Common Docker commands
Build image: `docker build -t menny/android_studio:latest .`

Pull from Docker Hub: `docker pull menny/android_studio:latest`

## How To Use
To work with the Android Studio inside this Docker image, you'll need to run it and direct its X11 connection to the local (host) machine X-Server.

_Note:_ The following instructions come from [here](https://fredrikaverpil.github.io/2016/07/31/docker-for-mac-and-gui-applications/) and [here](https://hub.docker.com/r/dlsniper/docker-intellij/).

I assume you have an X-Server, and it accepts network connections. 

### Running on Mac
If you have a Mac, you will need to install `xquartz`. Run:
```
brew cask install xquartz
```
Start `xquartz`:
```
open -a XQuartz
```
And allow network connections in the _Preferences_ window. More details [here](https://fredrikaverpil.github.io/2016/07/31/docker-for-mac-and-gui-applications/).
Add your machine's local IP to `xhost`:
```
ip=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')
/opt/X11/bin/xhost + $ip
```
And run Android Studio:
```
docker run -d -e DISPLAY=$ip:0 -v /tmp/.X11-unix:/tmp/.X11-unix menny/android_studio:1.8.1 /opt/android-studio/bin/studio.sh
```

### Running on Linux -- did not verify
Probably just:
```
docker run -d -e DISPLAY=$ip:0 -v /tmp/.X11-unix:/tmp/.X11-unix menny/android_studio:1.8.1 /opt/android-studio/bin/studio.sh
```

### Running on Windows
No idea.

## Pro Tip
The process above will give you a blank installation of Android Studio. It's a nice PoC, but the downside is that every time you start the Docker image, you start in the same blank state.<br/>
My work-flow is as follow:

1. Start the Docker image
2. Installation wizard will come up, follow all steps. Let it download what it needs.
3. At this point, you might want copy your `id_rsa` keys into the Docker image, that's in case you need those keys for cloning your repo.

```
docker exec -it hardcore_meninsky bash
```

This will open a `bash` terminal inside the running Docker image (use `docker ps --all` to find the image name, in my example the name was `hardcore_meninsky`). Run these commands:

```
mkdir -p /root/.ssh
chmod 0700 /root/.ssh
exit
```

Back in the host terminal, copy the ssh keys into the image:

```
docker cp ~/.ssh/id_rsa hardcore_meninsky:/root/.ssh/
docker cp ~/.ssh/id_rsa.pub hardcore_meninsky:/root/.ssh/
```

4. Checkout your repo using the _Import from source control_ option.
5. Once your repo is loaded, setup Android Studio with everything you need (plugins, settings, code style, etc).
6. You might also want to download the sources for the Android SDK. Maybe compile the app once to make sure everything is fine.
7. You might also want to setup the `local.properties` file in your checkout repo. Add the SDK and NDK paths:

```
ndk.dir=/opt/android-ndk-linux
sdk.dir=/opt/android-sdk-linux
```

8. Quit Android Studio.
9. In the host machine's terminal run `docker ps --all`. You'll see the Android Studio container (probably the top-most) in exited status. Copy it's name (last column). For example:

```
➜ docker ps --all
CONTAINER ID        IMAGE                        COMMAND                  CREATED             STATUS                         PORTS               NAMES
daffb7bbee3f        menny/android_studio:1.8.1   "/opt/android-stud..."   21 minutes ago      Exited (0) 11 seconds ago                          hardcore_meninsky
```

10. Commit that container into a new tag (let's say _warm_android_studio_): `docker commit hardcore_meninsky warm_android_studio`. This might take a while.
11. You're done! Next time, you can run your warm image: `docker run -d -e DISPLAY=$ip:0 -v /tmp/.X11-unix:/tmp/.X11-unix warm_android_studio`
12. Or you can also start the same container again: `docker start hardcore_meninsky`. This will start the container with all the changes you made.

### More Pro Tips

 - You can setup your container with `local.properties` values and copying rsa keys by calling `./setup_container.sh [container name] [repo folder]`.
 - You can use the execution script `docker_as.sh`. This script will start a new container from `menny/android_studio`, or restart an already created container.