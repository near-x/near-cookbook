# Localization

## Build DApp with Localization in Mind

In the Open Web world, we should keep in mind your apps will be accessed by users not only by users who speak English, but also people all around the world.

Considering around 30~40% of the users may actaully come from districts like China, Korea, Vietnam, etc., it's important to build your decentralized application with localization ready from the beginning. 

To be specific, DApp localization means:

1. **i18n**: Your DApp's user interface has i18n support from the beginning. So it costs minimal extra efforts to refator your code and add support to other languages. 
2. **Web app deployment**: Since the frontend of most DApps nowadays are actually web applications. The deployment of your frontend code, espeically the static sites with JAMStack (Next.js, Nuxt.js, etc.), should choose the appropriate CI/CD, and CDN solutions to make your site be able to be open in less than 3 seconds rather than 30 seconds which will scare away your users.
3. **RPC Node and Indexer**: For NEAR RPC Node, and indexers, choose the nodes or servers that are most appropriate for your infrastructure and users to reduce the latency and improve stability. 
4. **Decentralized Storage**: For decentralized storage solutions such IPFS, Arweave, Sia, Ceramic, etc. choose an appropriate service provider which help make your images, videos, etc. accessible globally by default without much extra efforts.
5. **Backend Services**: For any other dependent serivces, such as any helper services built with indexers, or other relevant data services, try to make the services accessible globally.


### Internalization (i18n)

We suggest the DApp developers to build your web application with i18n from the first day. 

#### React

You can use [`react-i18next`](https://react.i18next.com/) or any other frameworks you prefer if you're building with React or React Native.

Or you may use [`react-localize-redux`](https://ryandrewjohnson.github.io/react-localize-redux-docs/) in case you don't want to keep the language route such as `en`, `zh` in your URLs.

#### Vue

[vue-i18n](https://kazupon.github.io/vue-i18n/) may be a good solution for Vue developers, or you can simply use [nuxt/i18n](https://i18n.nuxtjs.org/) module if you're building with Nuxt.js.

#### Need Help with Translation

In case you need help about translation your DApp into any languages, create an issue [here](https://github.com/near-x/near-localize-dapp/issues/new) following the template, and we'll help connect you with the community to set up bounties and ask verified translators to help with the translation of your project.


### Web App Deployment

#### Static Site Deployment

If you're deploying your web app as a static site or SPA (Single Page Application), and would like to make your site easily accessible with fast loading speed in all areas including China, you need to carefully choose the deployment services, otherwise it's possible your users will complain why your sites take 30~60 seconds to open.

Unfortunately, the most popular static site hosting solutions simply doesn't work globally especially in China, including Netlify, Vercel, Render, etc.

The current recommended solution is [Cloudflare Pages](https://pages.cloudflare.com/) which has verified to be working well even if your JS bundle may be as large as 1M.


### RPC Nodes and Indexers

#### RPC Node

If your app requires to use custom RPC Nodes, NEAR Protocol is working with RPC Node service providers such as Figment DataHub, Bison Trails, BSN (Blockchain-based Service Network), NEAR DRPC, etc., which will help to set up the best solutions for your DApps. 

Please feel free to contact email: robert AT near DOT org if you need nodes from China, we can help to set up optimized RPC nodes for your DApps. 

#### Indexer

For most DApps, now you can use the public indexer server as specifid in the indexer chapter, but in case you need any customized Indexer server, you can try to set up your own or discuss with us in community. 


### Decentralized Storage

NEAR Procotol is working closely with Decentralized Storage solutions such as Filecoin, Ceramic, Crust, etc., to make it extremely easy to integrate your DApp with decentralized storage solutions. 

#### IPFS

You should look at the Filecoin-NEAR Bridge bulit by Textile if you're building with IPFS which provide free storage for NEAR users: https://near.storage/

Also we'd like to recommend you to look at the IPFS service providers such as ......, in case you'd like to have built-in CDN ready for your IPFS files. 


#### Ceramic

To Be Added

#### Arweave

To Be Added

### Backend Services

For backend services who'd like to achieve globally accessibility, you need to route your users to the optimal servers to reduce the latency. 



## Examples

1. Mintbase
2. Paras
3. Pulse
4. Ref Finance
5. Aurora
6. Rainbow Bridge
7. SputnikDAO




