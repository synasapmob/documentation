## Supported Versions:

- Only Ledger Nano S/X/S Plus with the USB protocol are supported.
- Root Ledger Wallets are accepted—sub-accounts are not.
- Tested successfully on version 1.4.3 (other versions are untested).

## Steps to Connect:

1. Plug your Ledger device into your computer via USB.
2. Open the "Solana" app on your Ledger. It should display "Application is ready."
3. Click "Connect Wallet" on MoonLaunch and select "Ledger Wallet."
4. Choose the wallet you’re interested in and sign the message on your Ledger.
5. Enjoy! 🚀 But remember the Root Ledger Wallet requirement (see point 2 of "Supported Versions").

## FAQ

### 1. Cannot sign the transaction?

   - You need to enable Blind Signing before signing the transaction.
      Follow this guide [Enable Blind Signature](https://support.ledger.com/article/4499092909085-zd])

   - Always keep your Ledger screen on and ensure the application displays 'Application is ready.' Please do not lock it.

### 2. Where can I find the "Root Ledger Wallet"?

   - When importing an account, you can select the Root Ledger Wallet instead of sub-accounts.

## PROBLEM/IMAGES

![Screen Shot 2025-02-12 at 10 13 50](https://github.com/user-attachments/assets/b9b0d3c2-e541-47fd-bf94-53171167dc70)

![Screen Shot 2025-02-12 at 09 57 20](https://github.com/user-attachments/assets/cc00f17d-9db6-4b5b-b77b-ae573da6bfca)

![Screen Shot 2025-02-12 at 09 50 29](https://github.com/user-attachments/assets/ae1d85c6-0443-49be-8fa6-68189de0e56c)

![Screen Shot 2025-02-12 at 10 25 14](https://github.com/user-attachments/assets/2742cf59-78e9-4081-ad53-2c50b2b22fc3)

![image](https://github.com/user-attachments/assets/a21c24a6-59d8-403c-9cb5-aab098ab6571)









## Tutorial

You can choose the type (HID/USB) for the Ledger device to use with the application. Also, note that the Ledger currently supports SOL, BTC, Polkadot, and more. [SEE MORE](https://github.com/ledgerhq#nano-applications)

#### 1. Installing Dependencies

```
Install the following packages:

  // Ledger Nano S/X/S Plus with the HID protocol
  https://www.npmjs.com/package/@ledgerhq/hw-transport-webhid

  // Ledger Nano S/X/S Plus with the USB protocol (we using it)
  https://www.npmjs.com/package/@ledgerhq/hw-transport-webusb

  // Using bs58 to 'decode' or 'encode'
  https://www.npmjs.com/package/bs58

  // Using hq-app-solana to sendTransaction
  https://www.npmjs.com/package/@ledgerhq/hw-app-solana

  // Using utils by
  https://www.npmjs.com/package/@project-serum/anchor
  https://www.npmjs.com/package/@solana/web3.js

```

#### 2. Connecting

```
  // Connect to your Ledger device (ensure it is plugged into your computer)
  const transport = await TransportWebUSB.create();

  // Get BIP32 Derivation Path and Public Key
  const from_derivation_path = solanaDerivationPath();
  const from_pubkey_bytes = await solanaLedgerGetPubkey(transport, from_derivation_path);
  const from_pubkey_string = bs58.encode(from_pubkey_bytes); // Ensure bs58 is installed; get it from https://www.npmjs.com/package/bs58

  console.log('Your public key bytes: ' + from_pubkey_bytes);
  console.log('Your public key string: ' + from_pubkey_string);

```

#### 3. Sign message

```
  // Serialize the message to send to the transport
  const message = Buffer.from("nonce authentication: 4000");
  const message_serialize = Buffer.concat([
    Buffer.from([255]),
    Buffer.from("solana offchain"),
    message
  ]);

  const payload = Buffer.concat([
    Buffer.from([1]),
    from_derivation_path,
    message_serialize
  ]);

  // Request to sign the message with your Ledger
  const INS_SIGN_OFFCHAIN_MESSAGE = 0x07;
  const signature = await transport.send(INS_SIGN_OFFCHAIN_MESSAGE, P1_CONFIRM, payload);

  console.log("Your signature: " + signature);
  console.log("Your message: " + bs58.encode(message_serialize));

```

#### 4. Sign transaction

```
  // Create a SOL transfer instruction
  const appSolana = new HQappSolana(transport); // Assuming HQappSolana is defined correctly
  const from_pubkey = new solana.PublicKey(from_pubkey_string);
  const to_pubkey = new solana.PublicKey("something"); // Replace with a valid recipient public key
  const tx_transfer = solana.SystemProgram.transfer({
    fromPubkey: from_pubkey,
    toPubkey: to_pubkey,
    lamports: 42
  });

  // Create and sign the transfer transaction
  const tx = new solana.Transaction({
    recentBlockhash: recentBlockhash, // Assumes recentBlockhash is defined (e.g., fetched from the network)
    feePayer: from_pubkey
  }).add(tx_transfer);

  const PATH_BIP32 = `44'/501'/1'`;
  const { signature } = await appSolana.signTransaction(
    PATH_BIP32,
    tx.compileMessage().serialize()
  );
  console.log("Your signature: " + signature);

  // Verify the transfer signature
  tx.addSignature(from_pubkey, signature);
  console.log("Signature verifies:", tx.verifySignatures());

  // Send the transaction (assumes connection is defined, e.g., new solana.Connection())
  const txId = await connection.sendRawTransaction(tx.serialize());
  console.log("Transaction ID: " + txId);

```
