# AI Foresight v1.1 – live source feed

## What changed
- News page has **Refresh live headlines**; fetches current RSS headlines through a Netlify Function, with sources and publication dates.
- Home page includes these headlines, and bookmarking/filtering works as before. Existing local storage (aiforesight-v1) is retained **if you deploy to the same domain**.
- If sources are unavailable, the app says so; sample cards remain marked SAMPLE.
- This is source aggregation, **not AI summaries**, real-time funding verification, push alerts, or the 8 AM ChatGPT task integration.

## Deploy (important)
Netlify Drop can deploy the static page but may not deploy the server-side Function from a ZIP. Use GitHub + Netlify's **Import an existing project**, or Netlify CLI (`netlify deploy --prod --build`) from this folder. In Netlify, the publish directory is the repository root (`.`) and functions folder is `netlify/functions` as configured in netlify.toml. No build command is required.

1. Extract this ZIP and upload its files to a new private GitHub repository (keep folder hierarchy).
2. Netlify dashboard > Add new project > Import existing project > GitHub > select repository.
3. Publish directory `.`, functions `netlify/functions`; leave the build command empty and deploy.
4. Open the new HTTPS URL, go to **AI News** and tap **Refresh live headlines**.
5. On Fold6, open Chrome > Install app. If deploying to your **existing Netlify site/domain**, reconnect that site to the repository and redeploy to preserve locally saved entries. A new domain uses separate browser storage.

## Safety
The live news endpoint is public and reads only public feeds. The app is not yet password-protected: do not put confidential data in it on publicly reachable devices, and do not expose API keys. The AI processing stage should use OpenAI API only from server-side functions with rate limiting, auth and quotas. OpenAI API is billed separately from ChatGPT Plus.

## Sources
Google AI, Hugging Face, TechCrunch AI, NVIDIA AI RSS. Some sources may block fetches or change feed URLs. The app reports errors; titles/descriptions are publishers' text, not verified factual conclusions.
