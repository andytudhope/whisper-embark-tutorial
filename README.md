Using Whisper with Embark
===

## Intro
In this tutorial we'll learn how to use Ethereum's Whisper protocol and Embark, creating a chat application even simpler than the one we built in our [previous tutorial](https://status.im/research/tutorial_basic_cli.html). Before cloning this repository, please make sure you have the latest version of Embark installed. Follow the instructions at https://embark.status.im/docs/installation.html.


## Setup
Once you have Embark installed, we are ready to begin coding! Execute these commands in a terminal window to clone the repository and install its dependencies

```
git clone https://github.com/status-im/whisper-embark-tutorial.git
cd whisper-embark-tutorial
npm install
```

## First run
Now let’s run our website quickly to see what Embark will do for us.
```
embark run
```
You should see the Embark console and it's components. 
* *Contracts* - the top left shows which contracts are deployed and their address.
* *Modules loaded and running* - the top right shows the status of the loaded modules running (or not running) in Embark. 
* *Log* - the middle shows log output. 
* *Console* - on the bottom row there is a console that will let us interact with `web3` and `ipfs` (try it out by typing `help` to see available commands).

You’ll notice from the logs and from the modules that Embark has started various processes, and webpacked our website for us. Let's take a tour of the barebones dApp. The website has several features that *are not yet hooked up to whisper*, but let’s take a look around at the website anyway. Launch `http://localhost:8000` in your browser.

## Coding our dApp

The file `./app/js/index.js` is full of `TODO`s that we need to work with. You'll notice that using Embark, the amount of boilerplate code is reduced, and you will only have to deal with you dApp's business logic. 

In this example, Embark generates a keypair for us. We'll only need to implement code for sending and receiving messages. This is done by using `EmbarkJS.Messages.sendMessage(options)`. Unlike using `web3.utils.shh` directly, With Embark it is not necessary to hex-encode the data. You can send plain text as we do here:

```
// Send message via whisper
EmbarkJS.Messages.sendMessage({
    topic: DEFAULT_CHANNEL, 
    data: message
});
```

`sendMessage` accepts an option object that can contain the following attributes:

* `symKeyID`, if specified, it will send the message to this symmetric key id. Otherwise, it will send to a random symmetric key generated by Embark (Either `symKeyID` or `pubKey` must be present. Can not be both.).  
* `pubKey` is the public key for message encryption. (Used when sending asymmetric messages). Either `symKeyID` or `pubKey` must be present. Can not be both.  
* `usePrivateKey` is a boolean that indicates if you're going to use an private key for signing the message, or use Embark's autogenerated random keypair.
* `privateKeyID` is required if you set `usePrivateKey` to `true`. Here you send the ID of the signing key.
* `ttl` is the time to live in seconds. Default value is `100`
* `powTime` is the maximal time in seconds to be spent on proof of work. Default value is `3`
* `powTarget` is the minimal PoW target required for this message. Default value is `0.5`
* `topic`, optional when using private keys. Can be an array of strings, or a string that contains the topic for the messages. Will be automatically encoded to a 4 bytes hex string(s).


#### `// TODO: Subscribe to whisper messages`
To receive the messages sent via Whisper, Embark offers a `EmbarkJS.Messages.listenTo(options)` that helps us obtain messages based in the `options` used to filter the messages. Let's implement this functionality, calling the function addMessage(data, time) each time a message is received:

```
// Subscribe to whisper messages
EmbarkJS.Messages.listenTo({topic: [DEFAULT_CHANNEL]}, (error, message) => {
    if(error){
        alert("Error during subscription");
        return;
    }

    const {data, time} = message;

    addMessage(data, time);
});
```

Just like with `sendMessage`, `listenTo` also accepts an option object, with the following attributes:
* `symKeyID`, if specified, it will send the message to this symmetric key id. Otherwise, it will send to a random symmetric key generated by Embark (Either `symKeyID` or `pubKey` must be present. Can not be both.).  
* `usePrivateKey` is a boolean that indicates if you're going to use an private key for signing the message, or use Embark's autogenerated random keypair.
* `privateKeyID` is required if you set `usePrivateKey` to `true`. Here you send the ID of the signing key.
* `topic`, optional when using private keys. Can be an array of strings, or a string that contains the topic for the messages. Will be automatically encoded to a 4 bytes hex string(s).
* `minPow` is the minimal PoW requirement for incoming messages.

After adding this code, open two instances of the chat application and write a message. You'll see how it gets displayed in both windows


## Final thoughts
Building a dApp that uses Whisper with Embark is something amazingly easy. You could easily extend this tutorial dapp to support private messages, just like we did in our previous tutorial for [Getting started with Whisper](https://status.im/research/tutorial_basic_cli.html).

Something that you must consider is that even through Embark lets you use Whisper in your dApps, it does not mean that anyone will be able to use it when browsing your dApp. This is due to this protocol not being enabled in most providers. You'll need to connect to a node that supports this feature (i.e. `geth` with the `--shh` flag). 

Things will change in the future once Whisper gains more traction. Let's work together in building dApps that communicate between each other via Whisper!
