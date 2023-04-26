<p align="center">
  <a href="https://fingerprint.com">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="resources/logo_light.svg" />
      <source media="(prefers-color-scheme: light)" srcset="resources/logo_dark.svg" />
      <img src="resources/logo_dark.svg" alt="Fingerprint logo" width="312px" />
    </picture>
  </a>
</p>
<p align="center">
  <a href="https://github.com/fingerprintjs/fingerprintjs-pro-gtm/actions/workflows/build.yml">
    <img src="https://github.com/fingerprintjs/fingerprintjs-pro-gtm/actions/workflows/build.yml/badge.svg" alt="Build status">
  </a>
</p>

## Repository content
1. FingerprintJS Pro Google Tag Manager template
2. FingerprintJS Pro adapter for Google Tag Manager

# FingerprintJS Pro Google Tag Manager template

You can find an example of using this integration on [our website](https://dev.fingerprint.com/docs/fingerprintjs-pro-google-tag-manager)

## Usage

1. You should have FingerprintJS account
2. Add FingerprintJS Pro Tag in your GTM admin panel (you can import it from [github repo](https://github.com/fingerprintjs/fingerprintjs-pro-gtm/blob/master/template.tpl))
3. Set up the public API key and choose the region
4. Use `FingerprintJSPro.loaded` event to get data from dataLayer. You can create trigger for this event.
5. Use `Result custom name` field to change the variable name for the result.

## Template Fields

You can find more info on the FingerprintJS Pro agent documentation page [https://dev.fingerprint.com/docs/js-agent](https://dev.fingerprint.com/docs/js-agent)

`Tag type` – The way you want to use the tag. There are 3 options:
  - `Load and identify` – the default behavior. Load the JS agent and identify the browser immediately. If you want to load the JS agent first and identify the browser later based on some event, use two separate `Load` and `Identify` Fingerprint Pro tags. 
  - `Load` – only load the JS Agent.
  - `Identify` – identify the browser. The JS Agent must be loaded before. You can collect additional metadata data first and then trigger this tag with the metadata inside `linkedId` or `tag`.

`Public API key` - Your public API key that authenticates the agent with the API.

`Region` - The [region](https://dev.fingerprint.com/docs/regions) of your subscription.

`Endpoint` - This parameter should only be used with the [Custom subdomain](https://dev.fingerprint.com/docs/subdomain-integration). Specify your custom endpoint here.

`tag` - a customer-provided value or an object that will be saved together with the identification event and will be returned back to you in a webhook message or when you search for the visit in the server API.

`linkedId` - is a way of linking current identification event with a custom identifier. This will allow you to filter visit information when using the [Server API](https://dev.fingerprint.com/docs/server-api)

`Extended result` - The response object includes a confidence score field representing the probability of accurate identification. The extended response object also includes several fields with useful timestamps related to a visitor. See more information on `firstSeenAt/lastSeenAt` timestamps [here](https://dev.fingerprint.com/docs/useful-timestamps).

`Result custom name` - you can specify the result field name in `dataLayer`

# FingerprintJS Pro adapter for Google Tag Manager

Since GTM ([Google Tag Manager](https://tagmanager.google.com/)) uses a subset of JavaScript API that doesn't support Promises, we created this adapter for the [FingerprintJS Pro JS agent](https://dev.fingerprint.com/docs/js-agent) that can be used in a GTM template.

Adapter code hosted on CDN and accessible via the next URL `https://opencdn.fpjs.sh/fingerprintjs-pro-gtm/v0/iife.min.js`

More information about CDN you can get in [CDN repository](https://github.com/fingerprintjs/cdn)

## Example of usage

In GTM template we can load FingerprintJS Pro this way:

```javascript
const injectScript = require('injectScript');
const callInWindow = require('callInWindow');
const url = 'https://opencdn.fpjs.sh/fingerprintjs-pro-gtm/v0/iife.min.js';

const onSuccess = () => {
  const onFpJsLoad = (result) => {
    const dataLayerPush = createQueue('dataLayer');
    dataLayerPush({
      event: 'FingerprintJS.loaded',
      visitorId: result.visitorId
    });
    data.gtmOnSuccess();
  };

  callInWindow('FingerprintjsProGTM.load', {apiKey: data.apiKey}, onFpJsLoad);
};

// If the script fails to load, log a message and signal failure
const onFailure = () => {
  data.gtmOnFailure();
};

injectScript(url, onSuccess, onFailure);

```

## Limitations

Some advanced JavaScript agent properties (`tlsEndpoint`, `disableTls`, and `storageKey`) are not currently supported. If you require to use these features in the GTM, please contact [support](mailto:support@fingerprintjs.com).

Ad-blocking browser extensions such as AdBlock, uBlock Origin, etc., can block all scripts served by Google Tag Manager, including Fingerprint. If this is a problem for your use case, see Google Tag Manager documentation for [Server-side tagging](https://developers.google.com/tag-platform/tag-manager/server-side) and [Custom domain configuration](https://developers.google.com/tag-platform/tag-manager/server-side/custom-domain).
