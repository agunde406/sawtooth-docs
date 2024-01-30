# Installing and Running Sawtooth

Before you can start developing for the *Sawtooth* platform,
you\'ll need to set up and run a local validator to test your
application against. Once running, you will be able to submit new
transactions and fetch the resulting state and block data from the
blockchain using HTTP and the Sawtooth
`REST API <../architecture/rest_api>`{.interpreted-text role="doc"}. The
methods detailed here will apply to the included example transaction
families, *IntegerKey* and *XO*, as well as any transaction families you
might write yourself.

Sawtooth validators can be run from prebuilt
[Docker](https://www.docker.com/) containers, installed natively using
[Ubuntu 16.04](https://www.ubuntu.com/) or launched in AWS from the [AWS
Marketplace](https://aws.amazon.com/marketplace/pp/B075TKQCC2). To get
started, choose the platform appropriate to your use case and follow one
of the installation and usage guides below.

::: toctree
docker.rst ubuntu.rst aws.rst
:::

<!--
  Licensed under Creative Commons Attribution 4.0 International License
  https://creativecommons.org/licenses/by/4.0/
-->
