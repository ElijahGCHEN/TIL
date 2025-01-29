# Overview
OpenID Connect (OIDC) is the most common SSO technology. OIDC is used to control access from outside of GCP in the form that Google will work as the IdP when users are trying to access applications (specifically applications built upon GCP in this article), and the users only need to get their Google credentials veried by Google instead of having a seperated credential for that application.

OpenID Connect is a much newer authentication layer, finalized in 2014, on top of OAuth 2.0. It uses JSON Web Tokens (JWTs, sometimes pronounced “jots”) instead of XML, and uses somewhat different terminology (“relying party” is usually used in OIDC versus “service provider” in SAML, for example).

Some services can take requests from OIDC-enabled applications and “translate” these to requests to a SAML IdP. In larger organizations, it’s very common to have both standards in use, and most IdPs support both.



