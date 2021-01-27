Here is how to add a new currency to WalletGenerator.net. This how-to is sufficiently detailed that you don't need to be a developer to follow it.

Why should you do that ? We are not a company, just two regular guys having fun on a side project. We have a regular job and it's not always easy to find time to work on it. As you will see, it's not that technical, but time consuming. Also, if we spend less time on adding new currencies, we will be able to spend it on new features.

Before starting, keep in mind that there is some currency that we cannot support. If the developers made some change in the address format, we won’t hack the crypto core of the project and take the risk to tamper the security of the others currencies. At least the following cannot be supported:
* GroestlCoin: use the Grøstl hash instead of SHA-256 for the checksum in the address

## Step one: download a local copy of the website

Download https://github.com/MichaelMure/WalletGenerator.net/archive/master.zip, extract it on your disk and open index.html with your browser.

## Step two: Find the prefixes for the address format of your currency

Each currency use a specific prefix in both public address and WIF private key as a marker of validity of the address. We need to find them to generate valid wallet for your currency. You could find those directly in the source code of software wallet, but it's easier to extract them from actual addresses. If you are curious, you can find more details here: https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses

1. Download and run a wallet software for your currency
2. Copy a public address (usually in the "receive" tab)
3. Go to https://walletgenerator.net/ or your local copy and open the development tools (usually F12) and go to the javascript console.
4. Run the following command with the address you just copied:
`"0x"+Bitcoin.Address.decodeString('DJYXr7uhXYVNoWnYxtvihJNHvzWXBZfG9G')[0].toString(16)`
You should get a hexadecimal number like 0x1e. This is the **network version**.
> If you get an error, you can try `Bitcoin.Address.decodeString('1Kbhqr4Q1V6FQA7CU5N32x5729JWBdoNjW').slice(0, -20).map(x => "0x"+ x.toString(16))`. Some currencies like ZCash use two number instead of one. In this case you should use both number like this:`[0x1c,0xb8]`. If you still have an error there is probably some changes in the address format and we won't be able to support it.
5. Now, we will need a private key in Wallet Import Format (WIF). Go to your software wallet and find the console (usually in the Debug window). Now run the following command with your own public address:
`dumpprivkey DJYXr7uhXYVNoWnYxtvihJNHvzWXBZfG9G`
This is you private key.
6. Go back to the javascript console on the website and run the following command with your private key:
`"0x"+Bitcoin.Base58.decode("6K2S1XZ2iAagtfMCMcp3PtuwiS2fAyvA9LfpaCYQGtjALCMaVbi")[0].toString(16)`
This is the **private key prefix**. Again, an error means "won't support".

## Step three: Find a good logo

Find a good logo for your coin and resize it to 300x300 pixels. Save it as a PNG file and copy it in the _logos_ directory. It's filename should be currency.png, all lowercase (ex: dogecoin.png).

## Step four: Choose a design

There is already a bunch of design variation in the _wallets_ directory. Choose one and copy it with the same name as the logo file. You can also go for your own design.

## Step five: Assemble the pieces

You can now edit the _src/janin.currency.js_ file and add your currency in the janin.currencies array at the end of the file, in the good alphabetical position:
`janin.currency.createCurrency ("MyAwesomeCurrency",0x1e, 0x9e, "", "" , ""),`

The first parameter is the name of your currency (it should be the same as the filenames you used, but capitalized as you like).
The second parameter is the **network version** you found earlier.
The third parameter is the **private key prefix** you found earlier.
Let the other parameters blank for now.

You now need to build the website. If you have grunt (http://gruntjs.com/) installed, you can just run the `grunt` command. Otherwise, you can copy your code line directly in the root _index.html_ file in the same position (beware, it's a big file, find your way with Ctrl+F "createCurrency").

## Step six: Find the two other values.

If you reload the website, you should already see your currency as supported and generate wallet, but we need to find the parameters 3 and 4. They are the first letter of a WIF address in classic and compressed format.

1. Go the Bulk wallet tab and generate a series (at least 100) of classic addresses (don't check "Compressed addresses?").
2. Look at the first letter of the generated private keys (third column). If it's always the same letter, this is your third parameter (ex: "6"). If there is two different letter, enclose them in brackets (ex: "[LK]"). This is case sensitive.
3. Do the same after checking "Compressed addresses?". This is the fourth parameter.
4. Complete your two lines of code in _src/janin.currency.js_ and _index.html_ with those values.

## Step seven: The donation address

The last parameter is the donation address for this currency. Since you just did the work, why not use your own ? If you wan to support WalletGenerator.net, you can also leave that blank and we will add one ourselves.

## Step eight: Test that

To validate all that, we need to check that everything went fine and that generated addresses are correctly imported in a software wallet.

1. Generate a couple of public address and private key in your new website
2. Go to the console of your software wallet and run the following command with your new private key:
`importprivkey 6Kkv52j7sk7eZ2ZezFqwCfFuccU7ouMdfYQcGCYuaFHtETow8nC`
3. Check in the UI that the corresponding public address appears correctly
4. Repeat that with a few addresses. If no error appears and all addresses match, all is well, nicely done !

## Step nine: Send us all that for inclusion

If you know your way with git and Github, we prefer a pull-request. Otherwise, you can send us (batolettre@gmail.com) your logo, wallet and line of code. We will add that in the live website ASAP.


