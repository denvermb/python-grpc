# gRPC Python App

### Steps to run the application
#### 1. Clone the application and open the `python-grpc` directory in VS Code

#### 2. Install dependencies
Run the following commands to set up your virtual environment and install all the necessary dependencies for the app.

```bash
python3 -m venv venv
```

This creates a virtual environment called venv that we'll use to isolate our dependencies. Before we do anything else though, we need to activate our virtual environment with the following command:

```bash
source venv/bin/activate
```

Now with our virtual environment active, we can install our dependencies with: 

```bash
pip3 install -r requirements.txt
```

#### 3. Run the app locally
To run this app locally, run `python3 greeter_server.py`. The gRPC service will start listening on port `8585`.The server app is now ready to receive requests from the client.

##### Start the client application
Once the application is running, you can start the client application. Run the client application with `python3 greeter_client.py`. 

The client app will open a console app and respond with the text:

```bash
Greeter client received: Hello, you!
Greeter client received: Hello again, you!
```

##### Deploying to App Service
Now that it's tested locally, you can deploy the application to App Service.  Create a web app using the following directions in this [How-To](https://github.com/Azure/app-service-linux-docs/blob/master/HowTo/gRPC/use_gRPC_with_dotnet.md#deploy-to-app-service) to enable gRPC calls.

Once it's deployed, you can replace the listening port in the local client application with the azurewebsites.net url to test the deployed grpc server. This is found on line 26 of `greeter_client.py`.

### Issues/Logs
1. Once deployed, the azurewebsites url shows an HTTP 503 error "Failed to load"
2. However, the log stream application logs show the following errors:

```
{"timestamp":"2022-07-11T04:56:51.032546763Z","level":"ERROR","machineName":"lw0sdlwk00016T","containerName":"python-grpc_0_7788ab5f","message":" Output is compressed. Extracting it...\n","id":"0d5f27d2-2470-4e9b-8161-eccdc9f3b161","instance":"761a092c6cbeec305dbcc6c6e744b006c4d173d9d935c8e17cb6b4c751c4957e"}
```

```
{"timestamp":"2022-07-11T04:56:51.039991952Z","level":"ERROR","machineName":"lw0sdlwk00016T","containerName":"python-grpc_0_7788ab5f","message":" Extracting '/home/site/wwwroot/output.tar.gz' to directory '/tmp/8da62f988ae7a83'...\n","id":"6aacd216-7e72-44d1-99cd-6b92922d1f28","instance":"761a092c6cbeec305dbcc6c6e744b006c4d173d9d935c8e17cb6b4c751c4957e"}
```

```
{"timestamp":"2022-07-11T04:56:53.969282020Z","level":"ERROR","machineName":"lw0sdlwk00016T","containerName":"python-grpc_0_7788ab5f","message":" App path is set to '/tmp/8da62f988ae7a83'\n","id":"6355c6a3-5d35-450d-82c9-50f3aef5765e","instance":"761a092c6cbeec305dbcc6c6e744b006c4d173d9d935c8e17cb6b4c751c4957e"}
```

3. Platform logs show container exposed at 8585, although the container never properly starts

```
{{"timestamp":"2022-07-11T04:54:16.321Z","level":"INFO","containerName":"python-grpc_0_58311c64","machineName":"lw0sdlwk00016T","message":"docker run -d --expose=8000 --expose=8585 --name python-grpc_0_58311c64 -e WEBSITE_SITE_NAME=python-grpc -e WEBSITE_AUTH_ENABLED=False -e WEBSITE_ROLE_INSTANCE_ID=0 -e WEBSITE_HOSTNAME=python-grpc.azurewebsites.net -e WEBSITE_INSTANCE_ID=761a092c6cbeec305dbcc6c6e744b006c4d173d9d935c8e17cb6b4c751c4957e -e WEBSITE_USE_DIAGNOSTIC_SERVER=True appsvc/python:3.8_20220315.5 python greeter_server.py \n","id":"085e8912-fa47-4071-9591-03df46c5bf56","instance":"761a092c6cbeec305dbcc6c6e744b006c4d173d9d935c8e17cb6b4c751c4957e"}}
```

```
{"timestamp":"2022-07-11T04:54:20.849Z","level":"ERROR","containerName":"python-grpc_0_58311c64","machineName":"lw0sdlwk00016T","message":"Container python-grpc_0_58311c64 for site python-grpc has exited, failing site start","id":"3a7219d1-8503-49a1-ac58-504bbbf4f1e2","instance":"761a092c6cbeec305dbcc6c6e744b006c4d173d9d935c8e17cb6b4c751c4957e"}
```

```
{"timestamp":"2022-07-11T04:54:20.942Z","level":"ERROR","containerName":"python-grpc_0_58311c64","machineName":"lw0sdlwk00016T","message":"Container python-grpc_0_58311c64 didn't respond to HTTP pings on port: 8000, failing site start. See container logs for debugging.","id":"7e7fa632-5f70-4aba-8c13-69e3196330a3","instance":"761a092c6cbeec305dbcc6c6e744b006c4d173d9d935c8e17cb6b4c751c4957e"}
```

4. When trying to call make a call from the client

```
Traceback (most recent call last):
  File "greeter_client.py", line 36, in <module>
    run()
  File "greeter_client.py", line 28, in run
    response = stub.SayHello(helloworld_pb2.HelloRequest(name='you'))
  File "/mnt/c/Users/dbrittain/python-grpc-sample/venv/lib/python3.8/site-packages/grpc/_channel.py", line 946, in __call__
    return _end_unary_response_blocking(state, call, False, None)
  File "/mnt/c/Users/dbrittain/python-grpc-sample/venv/lib/python3.8/site-packages/grpc/_channel.py", line 849, in _end_unary_response_blocking
    raise _InactiveRpcError(state)
grpc._channel._InactiveRpcError: <_InactiveRpcError of RPC that terminated with:
        status = StatusCode.UNAVAILABLE
        details = "failed to connect to all addresses"
        debug_error_string = "{"created":"@1657514369.130117392","description":"Failed to pick subchannel","file":"src/core/ext/filters/client_channel/client_channel.cc","file_line":3128,"referenced_errors":[{"created":"@1657514369.130116240","description":"failed to connect to all addresses","file":"src/core/lib/transport/error_utils.cc","file_line":163,"grpc_status":14}]}"
```
