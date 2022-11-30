---
id: user-sdk-installation
title: Installation
sidebar_label: Installation
description: Nightfall SDK installation process
keywords:
  - docs
  - polygon
  - nightfall
  - sdk
image: https://wiki.polygon.technology/img/thumbnail/polygon-nightfall.png
---

## Install SDK from NPM

With a client set up, add the Nightfall SDK as a dependency to your project:

```bash
npm install nightfall-sdk
```

To use the SDK, import `UserFactory`.
This will generate an instance of `User` that you should use to perform all the available operations.

```bash
import { UserFactory } from 'nightfall-sdk';

user = await UserFactory.create(userOptions);

const txReceipts = await user.makeDeposit(depositOptions);
```

Where _userOptions_ is of type _UserFactoryCreate_ and _depositOptions_ _UserMakeDeposit_. <br></br>
Check out the [example scripts](https://github.com/maticnetwork/nightfall-sdk#example-scripts) for a better understanding on how to use the SDK to capacity.
