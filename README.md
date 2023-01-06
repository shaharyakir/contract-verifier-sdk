# TON Contract Verifier SDK

## TL;DR
TON verified contract code viewer for func with code highlighting 

## Related repositories

This repo is a part of the following:

1. [contract-verifier-contracts](https://github.com/ton-community/contract-verifier-contracts) - Sources registry contracts which stores an on-chain proof per code cell hash.
2. [contract-verifier-backend](https://github.com/ton-community/contract-verifier-backend) - Backend for compiling FunC and returning a signature over a message containing the resulting code cell hash.
3. [contract-verifier-sdk](https://github.com/ton-community/contract-verifier-sdk) (this repo) - A UI component to fetch and display sources from Ton blockchain and IPFS, including FunC code highlighting.
4. [contract-verifier](https://github.com/ton-community/contract-verifier) - A UI app to interact with the backend, contracts and publish an on-chain proof.

## ⭐️ Features
- Fetches contract sources code from the ipfs via a sources.json url  
- Displays code navigator with code highlighting
- Customizable data fetching (IPFS GW, Ton API endpoint)

## 📦 Getting Started

### Step 1: Prepare DOM ###
Add this to your HTML structure
```html
<div id="myVerifierContainer">
    <div id="myVerifierFiles">
    </div>
    <div id="myVerifierContent">
    </div>
</div>
```

### Step 2: Activate widget library

#### Node
(Coming soon)

#### Browser
```html
<script src="https://cdn.jsdelivr.net/gh/shaharyakir/contract-verifier-sdk/build/index.min.js"></script>
<script>
    document.addEventListener("DOMContentLoaded", async function () {
        const codeCellHash = "/rX/aCDi/w2Ug+fg1iyBfYRniftK5YDIeIZtlZ2r1cA=";
        
        const ipfslink = await ContractVerifier.getSourcesJsonUrl(
            codeCellHash,
            { httpApiEndpoint: "https://scalable-api.tonwhales.com/jsonRPC" }
        );

        // means there exists a verified source for this contract
        if (ipfslink) {
            const sourcesData = await ContractVerifier.getSourcesData(ipfslink, (ipfs) => ipfs.replace("ipfs://", "https://myproj.mygateway-ipfs.io/ipfs/"));
            const theme = window.matchMedia && window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light";

            ContractVerifierUI.loadSourcesData(sourcesData, {
                containerSelector: "#myVerifierContainer",
                fileListSelector: "#myVerifierFiles",
                contentSelector: "#myVerifierContent",
                theme
            });
        }
    });
</script>
```

## ℹ️ Interface

### ContractVerifier
```typescript
interface GetSourcesOptions {
  verifier?: string, // Defaults to "orbs.com"
  httpApiEndpoint?: string, // Defaults to an Orbs L3 Ton Gateway
  httpApiKey?: string
}

// Returns an `ipfs://` prefixed URL if the given code cell hash has a corresponding source verifier contract 
async ContractVerifier.getSourcesJsonUrl(codeCellHash: string, options?: GetSourcesOptions): Promise<string | null>;


interface SourcesData {
  files: { name: string; content: string }[];
  commandLine: string;
  compiler: string;
  version: string;
  verificationDate: Date;
}
type IpfsUrlConverterFunc (ipfsUrl: string) => string;

// Returns file names and their source code content
async ContractVerifier.getSourcesData(sourcesJsonUrl: string | null, ipfsConverter?: IpfsUrlConverterFunc): Promise<SourcesData>;
```

### ContractVerifierUI

```typescript
ContractVerifierUI.loadSourcesData(sourcesData: SourcesData, opts: {
    containerSelector: string; // main container
    fileListSelector?: string; // if omitted, the file list will not be populated and the setCode function can be used instead to switch between files
    contentSelector: string; // code container
    theme: Theme; // "light" or "dark"
    layout?: Layout; // "row" or "column"
});

// To be used usually only if the file list is manually built
ContractVerifierUI.setCode(contentSelector: string, content: string);
```

## 👀 Demo
1. Clone this repo
2. Run `npm install`
3. Run `npm run build`
4. Navigate to `example/vanilla`
5. Run `npm install`
6. Run `npm link ../../`
7. Run `npm run dev`

## 💎 Customization

### CSS
The widget will attach the following classnames to its components:
```
contract-verifier-container
contract-verifier-files
contract-verifier-file
contract-verifier-code
```

Which can be customized, for example in the following manner:
```css
#myContentFull code {
    background-color: var(--code-viewer-background);
    font-family: Ubuntu Mono,monospace; 
}
#myFilesFull .contract-verifier-file.active {
    background-color: var(--code-viewer-background);
}
#myFilesFull .contract-verifier-file.active {
    background-color: var(--code-viewer-background);
    color: var(--body-text-color); 
}
#myFilesFull .contract-verifier-file:hover {
    background-color: var(--code-viewer-tab-inactive-background);
    color: var(--body-text-color); 
}
#myFilesFull .contract-verifier-file {
    background-color: var(--code-viewer-tab-inactive-background);
    color: #666;
}
```

## 📔 License

MIT
