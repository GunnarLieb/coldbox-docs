# Caveats & Gotchas

The most important gotchas in using the ColdBox proxy for remoting or even event gateways is pathing. Paths are totally different if you are using `expandPath()` or per-application mappings. Per-Application mappings can sometimes be a hassle for `onSessionEnd()`. So always be careful when setting up your paths and configurations for remoting. Try to always have the correct paths assigned and tested.
