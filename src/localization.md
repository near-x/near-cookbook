# Localization

## Build DApp with Localization in Mind

In the Open Web world, we should keep in mind your apps will be accessed not only by users who speak English, but also people all around the world.

Considering around 30~40% of the users may actaully come from districts like China, Korea, Vietnam, etc., it's important to build your decentralized application with localization ready from the beginning. 

To be specific, DApp localization means:

1. **i18n**: Your DApp's user interface has i18n support from the beginning. So it costs minimal extra efforts to refator your code and add support to other languages. 
2. **Web app deployment**: Since the frontend of most DApps nowadays are actually web applications. The deployment of your frontend code, espeically the static sites with JAMStack (Next.js, Nuxt.js, etc.), should choose the appropriate CI/CD, and CDN solutions to make your site be able to be open in less than 3 seconds rather than 30 seconds which will scare away your users.
3. **RPC Node and Indexer**: For NEAR RPC Node, and indexers, choose the nodes or servers that are most appropriate for your infrastructure and users to reduce the latency and improve stability. 
4. **Decentralized Storage**: For decentralized storage solutions such IPFS, Arweave, Sia, Ceramic, etc. choose an appropriate service provider which help make your images, videos, etc. accessible globally by default without much extra efforts.
5. **Backend Services**: For any other dependent serivces, such as any helper services built with indexers, or other relevant data services, try to make the services accessible globally.


### 1. Internalization (i18n)

We suggest the DApp developers to build your web application with i18n from the first day. 

#### React

You can use [`react-i18next`](https://react.i18next.com/) or any other frameworks you prefer if you're building with React or React Native.

Or you may use [`react-localize-redux`](https://ryandrewjohnson.github.io/react-localize-redux-docs/) in case you don't want to keep the language route such as `en`, `zh` in your URLs.

#### Vue

[vue-i18n](https://kazupon.github.io/vue-i18n/) may be a good solution for Vue developers, or you can simply use [nuxt/i18n](https://i18n.nuxtjs.org/) module if you're building with Nuxt.js.

#### Need Help with Translation?

In case you need help about translation your DApp into any languages, create an issue [here](https://github.com/near-x/near-localize-dapp/issues/new) following the template, and we'll help connect you with the community to set up bounties and ask verified translators to help with the translation of your project.


### 2. Web App Deployment

#### Static Site Deployment

If you're deploying your web app as a static site or SPA (Single Page Application), and would like to make your site easily accessible with fast loading speed in all areas including China, you need to carefully choose the deployment services, otherwise it's possible your users will complain why your sites take 30~60 seconds to open.

Unfortunately, the most popular static site hosting solutions simply doesn't work globally especially in China, including Netlify, Vercel, Render, etc.

The current recommended solution is [Cloudflare Pages](https://pages.cloudflare.com/) which has verified to be working well even if your JS bundle may be as large as 1M.


### 3. RPC Nodes and Indexers

#### RPC Node

If your app requires to use custom RPC Nodes, NEAR Protocol is working with RPC Node service providers such as Figment DataHub, Bison Trails, BSN (Blockchain-based Service Network), NEAR DRPC, etc., which will help to set up the best solutions for your DApps. 

Here are some RPCs that you can use for free:
- NEAR official public RPC:
  - mainnet: https://rpc.mainnet.near.org
  - testnet: https://rpc.testnet.near.org
- BlockPI DRPC:
  - mainnet: https://public-rpc.blockpi.io/http/near
  - testnet: https://public-rpc.blockpi.io/http/near-testnet

And if you are worried about rate-limiting and accessibility issues, please consider those RPC service providers:
- Bison Trails: https://bisontrails.co/near/
- Figment: https://www.figment.io/datahub/near
- BSN: https://bsnbase.io/g/main/index

Please feel free to contact email: robert AT near DOT org if you need nodes from China, we can help to set up optimized RPC nodes for your DApps. 

#### Indexer

For most DApps, now you can use the public indexer server as specifid in the indexer chapter, but in case you need any customized Indexer server, you can try to set up your own or discuss with us in community. 


### 4. Decentralized Storage

NEAR Procotol is working closely with Decentralized Storage solutions such as Filecoin, Ceramic, Crust, etc., to make it extremely easy to integrate your DApp with decentralized storage solutions.     

Checkout [this](https://docs.near.org/docs/concepts/storage-solutions) for more details.    

#### - IPFS

You should look at the Filecoin-NEAR Bridge bulit by Textile if you're building with IPFS which provide free storage for NEAR users: https://near.storage/

In order to provide a stable and fast access of IPFS to your users, we **highly recommend** you to use a Gateway/CDN service of IPFS, here are some options in the market:

- Fleek: https://fleek.co/
- Pinata: https://www.pinata.cloud/

In case none of those fits your need, please consider to either run a decicated IPFS node and pin all the resources your product need, or use a traditional CDN service like AWS CloudFront and upload a copy of all your resources to it.

#### - Sia

https://docs.near.org/docs/concepts/storage-solutions#sia

#### - Arweave

https://docs.near.org/docs/concepts/storage-solutions#arweave

### 5. Backend Services

Although having a centralized backend service somehow violates the concept of decentralization, it could dramatically increase site accessibility and usability for your users.         

Thus, generally speaking, we would encourage each product to have a well-maintained backend service which provides most frequently used data to frontend clients.    

When building a backend service, you should use multiple instances and have a load balancer to avoid single-point-failure issue. Additionally if you are using a cloud provider like AWS, multi AZ deployment is always a good option.


## Examples

1. Mintbase
2. Paras
3. Pulse
4. Ref Finance
5. Aurora
6. Rainbow Bridge
7. SputnikDAO
