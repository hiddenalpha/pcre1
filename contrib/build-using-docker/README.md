
Showcase how to build and install
=================================

Sometimes happy developers (like me) have no choice but using horribly
restricted systems where setting up tools to run even something as simple as
configure/make/install becomes a nightmare. I found it to be easier to have a
Dockerfile to build on a totally unrelated machine (but where I have the needed
privileges) and then just copy-paste the built result over to where I need it.


## Setup variable to reduce annoying repetitions

```sh
IMG=pcre1-demo:latest
```

## Make and install dockerimage

```sh
curl -sSL https://github.com/hiddenalpha/pcre1/raw/master/contrib/build-using-docker/Dockerfile | sudo docker build . -f - -t "${IMG:?}"
```


## Grab distribution archive

Most probably we wanna get the distribution archive. We can copy it out the
dockerimage to our host using:

```sh
sudo docker run --rm -i "${IMG:?}" sh -c 'true && cd dist && tar c *' | tar x
```

