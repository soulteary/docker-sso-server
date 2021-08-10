# CHANGELOG

## 1.0.0-2021.08.11

> Make the program run easier, only need to configure four parameters, you can experience the out-of-the-box SSO service.

-[New] Support loading license agreement according to specified path.
-[Improved] Simplify the parameter configuration, the SESSION_SECRET parameter will be automatically filled with a strong enough random string by default.
-[Improved] Simplify the parameter configuration, the CLIENT_NAME parameter does not need to be configured by default, and the service name specified by the user is used by default.
-[Improvement] Simplified parameter configuration, PORT parameters do not need to be configured by default, and port `80` is used by default in the container, which is convenient for internal calls in the container network environment.
-[Improved] Improve the output content when the service starts:
  -Clarify the scope of authorized use of the program, and provide single-user unlimited services for blog readers.
  -The configuration information used by the current application after desensitization is output by default.
-[Adjustment] Adjust the Docker container name and version number, and shorten the application container name.