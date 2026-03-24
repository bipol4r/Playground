# XXL-JOB-ADMIN Deserialization Vulnerability

XXL-JOB-ADMIN Version: 1.9.2 

## Root Cause of the Vulnerability

The vulnerability exists in the `JobApiController.doInvoke()` method which deserializes user-controlled input:

```java
private RpcResponse doInvoke(HttpServletRequest request) {
    try {
        byte[] requestBytes = HttpClientUtil.readBytes(request);
        if (requestBytes == null || requestBytes.length == 0) {
            // Returns error response for empty input
        }
        
        // Vulnerable deserialization point
        RpcRequest rpcRequest = (RpcRequest) HessianSerializer.deserialize(requestBytes, RpcRequest.class);
        return NetComServerFactory.invokeService(rpcRequest, null);
        
    } catch (Exception e) {
        // Exception handling does not prevent RCE
        logger.error(e.getMessage(), e);
        RpcResponse response = new RpcResponse();
        response.setError("Server-error:" + e.getMessage());
        return response;
    }
}
```

The `HessianSerializer.deserialize()` method uses `Hessian2Input.readObject()` without any class whitelist or validation, allowing attackers to deserialize arbitrary Java objects and trigger gadget chains.

## Setup Environment

Execute the following command to start a vulnerable environment.

```bash
docker compose build
docker compose up -d
```

The admin interface will be available at http://localhost:8080
