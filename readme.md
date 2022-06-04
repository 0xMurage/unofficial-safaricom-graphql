# Unofficial Safaricom GraphQL

### Prerequisites

- Requests can only be done via internet connection provided by a Safaricom simcard.
- Due to browser cross-origin requests limitation, the endpoint will not work without server proxy
  in a browser.
- Cookies generated should be submitted for all subsequent requests.
  They hold the [current] user's id.

### Requests Order

1) One must first request a access token.

```graphql
query {
    generateToken {
        status
        message
        token
        __typename
    }
}
```

The access token must be passed as header value for ``hetoken`` key for all
subsequent requests.

2) Request Safaricom to generate an OTP that will be sent via SMS

```graphql
query {
    generateOTP {
        status
        message
        __typename
    }
}
```

3) Submit the OTP for validation and session authentication

```graphql
mutation validateOTP($otp: Int!) {
    validateOTP(otp: $otp, page: "/account") {
        status
        message
        extraDetails
        __typename
    }
}
```

4) Retrieve requests token

```graphql
query {
    getToken {
        status
        message
        token
        proceed
        hash
        __typename
    }
}
```

5) To check if account is still authenticated

```graphql
query {
    isAuthenticated(page: "/account") {
        status
        __typename
    }
}
```

6) Run the required query e.g. ``getCustomerInfo`` to retrieve account summary

**N.B**: 
- Repeat steps 4 and 5 above before each subsequent query. If user is not authenticated, 
start from step 1.
- Some queries may require OTP session cookies before request e.g. ``consumeVoucher`` and ``sambaza``.
- M-pesa queries requires service pin session cookies provided by  ``validateServicePinSession``