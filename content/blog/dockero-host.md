+++
date = '2026-02-03'
title = 'Dockero in the Local Development Environment'
summary = 'How to configure Dockero to work with alternative Docker runtimes in development'
+++

I've been using the excellent [Dockero](https://github.com/the-abra/dockero) library for running some Docker containers in my web applications. In production environments the applications are running on the actual Docker runtime, but in my development environment I like to use alternatives.

Specifically, when I started using macOS again recently, I first opted for [Colima](https://github.com/abiosoft/colima) for its simplicity. However, anything involving Docker in my local development environment broke, and it took me a while to figure out what was wrong.

It took a while to figure out that the Dockero expects the Docker Unix socket to be at `/var/run/docker.sock`, but Colima has it at `~/.colima/default/docker.sock`.

To make Dockero work, you have to set the `DOCKER_HOST` environment variable. For my [Express](https://expressjs.com/)-based application this meant adding to `.env` (no `$HOME` variable substitution here unfortunately):

```properties
DOCKER_HOST=unix:///Users/lptr/.colima/default/docker.sock
```

I also had some tests using [Jest](https://jestjs.io/) that involved executing Docker containers. Fixing those was a bit more complicated.

I first installed [`dotenv`](https://github.com/motdotla/dotenv):

```shell
npm install dotenv --save-dev
```

Then in `jest.config.ts` I added:

```ts
import type { Config } from 'jest';

export default {
  // ...
  setupFiles: ['./jest.setup.ts'],
} as Config;
```

And in `jest.setup.js`:

```ts
import { config } from 'dotenv';

config({
  path: '.env.test',
  quiet: true,
});
```

This loads a `.env.test` file before tests executed by Jest, so I can also put the `DOCKER_HOST` in there.

I then later switched from Colima to [Rancher Desktop](https://rancherdesktop.io/). To make things work once again I had to change both `.env` files:

```properties
DOCKER_HOST=unix:///Users/lptr/.rd/docker.sock
```
