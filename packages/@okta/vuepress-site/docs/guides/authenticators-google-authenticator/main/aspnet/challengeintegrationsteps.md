### 1 - 4: Sign-in and Select Authenticator
The challenge flow follows the same first four steps as the [Enrollment flow](/docs/guides/authenticators-google-authenticator/aspnet/main/#integrate-sdk-for-authenticator-enrollment):

* Build a sign-in page on the client
* Authenticate the user credentials
* Handle the response from the sign-in flow
* Display a list of possible authenticator factors

### 5: Check Authenticator Status
When the user selects the Google Authenticator factor and clicks **Submit**, the form posts back to the `SelectAuthenticatorAsync` method. This checks whether the user is in _Challenge Flow_ or _Enrollment Flow_.

When in Challenge Flow, a call is made to `idxClient.SelectChallengeAuthenticatorAsync`, using its `selectAuthenticatorOptions` parameter to pass in the Google Authenticator factor ID.

```csharp
var selectAuthenticatorOptions = new SelectAuthenticatorOptions
{
    AuthenticatorId = model.AuthenticatorId,
};

selectAuthenticatorResponse = await _idxClient.SelectChallengeAuthenticatorAsync(
    selectAuthenticatorOptions, (IIdxContext)Session["IdxContext"]);
```

If the call is successful, the returned `selectAuthenticatorResponse` object has an `AuthenticationStatus` of `AwaitingAuthenticatorVerification` and the Challenge Page should be displayed.

```csharp
Session["IdxContext"] = selectAuthenticatorResponse.IdxContext;

switch (selectAuthenticatorResponse?.AuthenticationStatus)
{
    case AuthenticationStatus.AwaitingAuthenticatorVerification:
        var action = (model.IsWebAuthnSelected)
            ? "VerifyWebAuthnAuthenticator"
            : "VerifyAuthenticator";
        if (model.IsWebAuthnSelected)
        {
            Session["currentWebAuthnAuthenticator"] =
                selectAuthenticatorResponse.CurrentAuthenticatorEnrollment;
        }
        return RedirectToAction(action, "Manage");

    // other case statements

    default:
        return View("SelectAuthenticator", model);
}

```

### 6: Get one-time password from Google Authenticator
The user’s copy of Google Authenticator now displays the time-based one-time password for the newly added account which they will enter into a challenge page.

![A one-time password being shown in Google Authenticator](/img/authenticators/authenticators-google-one-time-password.png)

### 7 - 8: Display challenge page and process password
The challenge flow now follows the same final steps as the [Enrollment flow](/docs/guides/authenticators-google-authenticator/aspnet/main/#integrate-sdk-for-authenticator-enrollment):

* Display challenge page
* Process the one-time password