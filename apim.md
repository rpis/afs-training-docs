


```
<policies>
    <inbound>
        <base />
        <set-backend-service id="apim-generated-policy" backend-id="afs-simple-function" />
        <authentication-managed-identity resource="dd24e04c-df6f-4232-977c-e3587387ce01" output-token-variable-name="msi-access-token" ignore-error="false" />
        <!--Application (client) ID of your own Azure AD Application-->
        <set-header name="Authorization" exists-action="override">
            <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
        </set-header>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>

```


https://azure.github.io/apim-lab/

https://github.com/im2nguyen/rover
