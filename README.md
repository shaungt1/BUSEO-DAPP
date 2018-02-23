# Angular-DAPP

This Is how you would set up and Angular DAPP I am working on new branches and sections to add more Angular dependancies such as forms and useing #Ganche as oppssed to mettamask.
With a Angualr DAPP we can use reactive forms or form modules templates to connect all users login and reg info into the blockchain itself makieng it 100% decentrilized. The Idea is to get all datat decentrilized through the Nodes and the block chains. I will be adding another work up to connect Angular to the STEEM block chian and to  Ripple!

If you have any sugestions please submit your thoughts and ideas so I can add them.

# Setting up Angular 2/4 with Truffle can be done as follows:

# 1- Initialize a new Angular project
````
$ ng new myDapp
This will setup the most basic Angular folder structure.
````

# 2- Cd into myDapp and initialize truffle.
````
$ cd myDapp
$ truffle init
````
This will install truffle dependencies and create the truffle config file and contracts/migrations folders.

# 3- Install truffle-contract package to be able to make calls/transactions with truffle.
````
$ npm install truffle-contract --save
````
4- Using truffle with Angular
a) Import web3, truffle and contracts you'll be using. 
(I'm writing this example using the default MetaCoin contract that comes with truffle)
I will be adding how to connect to ganche Soon!!

# In app.component.ts:
````
import { Component, HostListener, NgZone } from '@angular/core';
const Web3 = require('web3');
const contract = require('truffle-contract');
const coinArtifacts = require('../../build/contracts/MetaCoin.json');
declare var window: any;
````
It's probable you'll get an error with require. If that happens, in typings.d.ts, add to the end of the file:
````
declare var require: any;
````
# b) Back in app.component.ts.
````
export class AppComponent {
  Coin = contract(coinArtifacts);
  coinInstance:any;

  account: any;
  accounts: any;
  web3: any;
}
````
# c) Add the following functions to AppComponent

````
constructor(private _ngZone: NgZone) {}
  @HostListener('window:load')
  windowLoaded() {
    this.checkAndInstantiateWeb3();
    this.onReady();
  }
    checkAndInstantiateWeb3 = () => {
    // Checking if Web3 has been injected by the browser (Mist/MetaMask)
    if (typeof window.web3 !== 'undefined') {
      console.warn(
        'Using web3 detected from external source. If you find that your accounts don\'t appear or you have 0 MetaCoin, ensure you\'ve configured that source properly. If using MetaMask, see the following link. Feel free to delete this warning. :) http://truffleframework.com/tutorials/truffle-and-metamask'
      );
      // Use Mist/MetaMask's provider
      this.web3 = new Web3(window.web3.currentProvider);
    } else {
      console.warn(
        'No web3 detected, falling back to Infura Ropsten'
        //'No web3 detected. Falling back to http://localhost:8545. You should remove this fallback when you deploy live, as it\'s inherently insecure. Consider switching to Metamask for development. More info here: http://truffleframework.com/tutorials/truffle-and-metamask'
      );
      // fallback - use your fallback strategy (local node / hosted node + in-dapp id mgmt / fail)
      this.web3 = new Web3(
        new Web3.providers.HttpProvider('http://localhost:8545')

      );
    }
  };
  onReady = () => {
    // Bootstrap the MetaCoin abstraction for Use.
    this.Coin.setProvider(this.web3.currentProvider);

    // Get the initial account balance so it can be displayed.
    this.web3.eth.getAccounts((err, accs) => {
      if (err != null) {
        alert('There was an error fetching your accounts.');
        return;
      }

      if (accs.length === 0) {
        alert(
          'You are not connected to an Ethereum client. You can still browse the data, but you will not be able to perform transactions.'
        );
        return;
      }
      this.accounts = accs;
      this.account = this.accounts[0];

      // This is run from window:load and ZoneJS is not aware of it we
      // need to use _ngZone.run() so that the UI updates on promise resolution
      this._ngZone.run(() =>{
        //Initial loading of UI
        //Load balances or whatever
      });

    });

  };
  ````
 That's all you need to get started.
 
- windowLoaded() method will make sure everything is set up after Metamask is loaded.

- checkAndInstantiateWeb3() initializes web3 object

- onReady() gets the accounts / account connected to the Dapp and makes the contract abstraction available.

Now you can use truffle with Angular. Just make sure testrcp is running and then do:
````
$ truffle compile
$ truffle migrate
$ ng serve
````
