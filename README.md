# Explainer: Opener Storage Partitioning

## Introduction

This proposal seeks to prevent or limit same-origin cross-frame communication that can bypass [storage partitioning](https://developer.chrome.com/en/docs/privacy-sandbox/storage-partitioning/), and to do so in alignment with existing work on the [Cross-Origin-Opener-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy).

## Background

[Storage partitioning](https://developer.chrome.com/en/docs/privacy-sandbox/storage-partitioning/), [shipping in M115](https://developer.chrome.com/en/blog/storage-partitioning-dev-trial/), partitions the first and third party storage buckets (and some communication APIs like BroadcastChannel, SharedWorker, and WebLocks) for a given origin. Storage partitioning seeks to prevent “certain types of side-channel cross-site tracking.”

The [Cross-Origin-Opener-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy) provides a way for a window to sever communication with cross-origin windows it opens or was opened by. A [proposed extension](https://docs.google.com/document/d/1qXlC6HZXd6UDokI8_cHYAVaXhHop0Ia6-z3fZl6saX8/edit#heading=h.c1dd4bjnvwc6) (restrict-properties) of this proposal allows for a mode with limited communication (asynchronous messages and detecting if the window was closed) between a popup and its opener.

The former proposal is primarily concerned with user privacy (e.g., preventing the average user from having their browsing unknowingly tracked in order to serve ads) whereas the latter proposal is primarily concerned with user security (e.g., ensuring even a targeted user cannot have information leaked cross-site).

The ability of windows to communicate with each other is critical to current web infrastructure, especially for payment and login models. Whatever changes are pursued in this space must proceed with care to avoid breaking the web.

## Threat Model

This section details the two threats we’re concerned with, ordered by the breadth of communication enabled by each. If synchronous scripting is possible that implies asynchronous communication is possible, but if asynchronous communication is possible that doesn’t imply synchronous scripting will be possible.

### Synchronous Scripting

If a user visits example.com which embeds an iframe for notexample.com, this iframe can open a new window for notexample.com.
![](https://github.com/arichiv/opener-storage-partitioning/assets/666538/4bebdcfa-8f69-4a19-acf3-a3f34bb79913)

