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

  // Get BIP32 Derivation Path and Public Key, reference from https://github.com/ByteZhang1024/wallet-adapter/blob/feat/add-sign-message-for-ledger/packages/wallets/ledger/src/util.ts#L8
  const from_derivation_path = getDerivationPath();

  // this using method 'send' of transport to get pubkey, reference from https://github.com/ByteZhang1024/wallet-adapter/blob/feat/add-sign-message-for-ledger/packages/wallets/ledger/src/util.ts#L143
  const from_pubkey_bytes = await getPublicKey(transport, from_derivation_path);

  // Ensure bs58 is installed; get it from https://www.npmjs.com/package/bs58
  const from_pubkey_string = bs58.encode(from_pubkey_bytes);

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
