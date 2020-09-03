---
title: Deploy your application to the Filecoin testnet
description: We recommend all developers deploy their applications to the Filecoin testnet. Here's a guide that shows you how.
---

# Deploy your application to the Filecoin testnet

If you have built your application and tested it on smaller-scale Filecoin networks (e.g. a [local network](https://docs.filecoin.io/build/start-building/interacting-with-the-network/#running-a-local-network) or the [Filecoin Nerpa network](https://docs.filecoin.io/build/start-building/interacting-with-the-network/#devnets)), you are now ready to deploy your application to the Filecoin testnet. We currently recommend all developers deploy their applications to the Filecoin testnet for many reasons, one of which is that this network is actively being monitored by the Protocol Labs team.

The Filecoin testnet is a network with parameters and activity levels as close as possible to the expected early state of the Filecoin main network (mainnet). The testnet is currently being used for the Filecoin testnet incentives, or [Space Race](https://docs.filecoin.io/mine/spacerace/), competition. As a developer on the Filecoin testnet, there are some important things you should know about development on the testnet.

::: tip
If you are using a hosted service like [hosted Powergate](https://blog.textile.io/prepare-to-launch-expanding-free-access-to-filecoin-through-hosted-powergates/) or [Textile Buckets](https://docs.textile.io/buckets/), you don’t need to worry about any of this. Textile’s services are soon to be deployed against the Filecoin testnet, so if you are using hosted Textile products, your application will also be deployed on the Filecoin testnet.

If you would like accelerated access to a hosted Powergate instance, please fill out [this form](https://forms.gle/f5Vd5kTNYTKrmj1D8). We will work with the Textile team to get you access to a hosted Powergate asap.
:::

**If you are managing your own Filecoin nodes, whether your own lotus node or Powergate node, please read on for these important details.**

## Join the testnet

The high-level steps required to join the testnet are:

1. [**Install lotus or Powergate.**](https://docs.filecoin.io/how-to/install-filecoin/) While the current installation instructions say to use a machine with 8GB memory and 4 cores, we find that installation on a machine with 32GB memory and 8 cores leads to much faster and more successful chain sync (step 3). Therefore, we recommend the following starter configuration:

- Type: Standard
- CPU Type: Shared CPU
- vCPUs: **8 vCPUs** (very important)
- Memory: **32 GB** (very important)
- SSD: 160 GB

Our teammates have also had success running `m5ad.2xlarge` instances on AWS. In order to significantly boost the initial chain sync speed, make sure to keep `~/.lotus` on a fast drive (ideally a SSD -- nvme is best!) and consider temporarily applying the patch listed in [this issue comment](https://github.com/filecoin-project/lotus/issues/3263#issue-684587473).

2. [**Run the lotus daemon.**](https://docs.filecoin.io/how-to/join-a-network/#get-started)

3. [**Wait for the Filecoin chain to sync.**](https://docs.filecoin.io/how-to/join-a-network/#chain-sync) This process can be extremely unwieldy and lengthy. At present, chainsync takes about 1/50th of the lifetime of the network to complete. That is, a network 100 days old will take about 2 days to sync from scratch. Efforts are underway to significantly reduce this! We highly recommend using a beefy machine (such as the hardware specs listed on this page) in order to get to the testing stage in a reasonable timeframe.

4. [**Create your first wallet address.**](https://docs.filecoin.io/how-to/join-a-network/#create-your-first-address) You need to create a wallet address in order to create storage deals on the Filecoin network. Remember that wallet address for future steps.

5. [**Request funds from the testnet faucet.**](https://spacerace.faucet.glif.io/) You need some FIL tokens in your testnet wallet in order to make storage deals on the network. You can request some test tokens in your testnet wallet by going to the testnet faucet, connecting your GitHub account, and then making the funds request.

6. [**Check your testnet wallet balance to make sure it contains >0 FIL.**](https://docs.filecoin.io/how-to/join-a-network/#check-wallet-address-balance) You will not see any funds in your testnet wallet until your node is fully synced (which may take several days, as noted above). Once you have a >0 FIL balance in your testnet wallet, you can proceed to the next step.

If you are running into any issues joining the testnet, please join the [Filecoin Slack](http://filecoin.io/slack) and leave a message in the [#fil-storage-dev channel](https://app.slack.com/client/TEHTVS1L6/CRK2LKYHW). Our team will respond asap in the channel.

## Make deals with Filecoin miners

Most miners on the current testnet are focused on onboarding as much storage capacity as possible, and are less focused on accepting storage deals from users on the network. In fact, many miners are currently filtering out deal proposals from storage clients so they are not distracted from their Space Race objectives.

In order to store your data on the Filecoin network, you need miners who are available to accept your storage deal proposal and store your data. Currently there are **two options**.

### Option 1: Protocol Labs Filecoin miners (recommended)

The Protocol Labs Filecoin team is maintaining a few high-availability miners on the Filecoin testnet. These miners will only accept deals from whitelisted addresses. In order to make successful storage deals with these miners, please fill out this [Google Form](https://forms.gle/f5Vd5kTNYTKrmj1D8) and include the node address from which you will be making your storage deals. If your Google Form submission is valid, within 24 hours of your form submission, you will receive a message on Slack/email from a Protocol Labs teammate notifying you that your node has been whitelisted. You will then be able to freely make deals with these Filecoin miners.

The miners are available at the following addresses (more may be added over time). You will only be able to make deals with these miners if you have been whitelisted according to the process above.

```
t016303
t016304
t016305
t016306
t016309
```

A few notes on these miners:

- These miners are running 32GB sectors exclusively. One 32GB sector takes ~12 hours to seal on average. Because of the sector size limitation, [any files larger than 32GB](https://docs.filecoin.io/how-to/store/prepare-data/#preparing-data) will need to be split into chunks and will be stored on multiple sectors.
- These miners will be storing unsealed copies of your data in addition to the sealed copies. This makes it possible to retrieve data quickly starting almost immediately after your deal has been accepted and your data has successfully transferred to the miner machine.
- These miners are configured to wait for ~8 hours after receiving the first storage deal proposal before they will start sealing the sector. Individual storage deal proposals are often for very small data sizes, and miners often pack several smaller deals into one 32GB sector before starting the time-consuming and expensive sealing process. If you make a storage deal that fills the whole 32GB sector (i.e. store a ~32GB file), the sealing process will start immediately (there will be no 8 hour lag before the start of the sealing process). If you are making a smaller storage deal, the impact is that a storage receipt of your storage deal will only make it onto the Filecoin chain ~20 hours (8 hours for the sealing wait time and 12 hours for sealing) after your storage deal was made.

### Option 2: Community miners

One Filecoin community member maintains a list of miners on the testnet who are currently accepting storage deals. You can try your luck with these miners as well.

- [List of miners](https://github.com/jimpick/workshop-client-testnet/blob/spacerace/src/annotations-spacerace.js)
- In this list, `active` means a deal got to the `active` state, i.e. it was successfully sealed into a sector that appeared on chain.
- In this list, `sealing` means a deal got to the `sealing` state, i.e. the deal got through the initial funds and data transfer stages and was accepted by a miner to be sealed into a sector. If all goes well, `sealing` stage deals get to `active` state. However, currently, it is not the case that all deals that have successfully made it to the `sealing` stage also make it to the `active` stage. We estimate that currently only about 20-30% of `sealing` deals eventually become `active`.
- This list is frequently updated, so keep an eye out for new miners who appear on the list.

## Support and troubleshooting

If you run into any issues with any of these steps, please join the [Filecoin Slack](http://filecoin.io/slack) and leave a message in the [#fil-storage-dev channel](https://app.slack.com/client/TEHTVS1L6/CRK2LKYHW). Our team will respond asap in the channel.

In general, the best way to get support from the Filecoin team is:

- Join [Filecoin Slack](http://filecoin.io/slack) and post a message in the [#fil-storage-dev channel](https://app.slack.com/client/TEHTVS1L6/CRK2LKYHW).
- _If you haven’t received a message within 24 hours_, please tag `@pooja` directly in your message thread.
- If the issue persists or cannot be solved by the support team, please [create an issue in the lotus repo](https://github.com/filecoin-project/lotus/issues/new). Include reproducible steps and any log outputs in the issue itself so we can help debug.

Happy building!
