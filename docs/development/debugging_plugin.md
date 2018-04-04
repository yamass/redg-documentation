# Debugging the Maven Plugin

If you find problems during the code generation with the Maven plugin, take these steps to debug the redg-generator running inside the plugin:

 - Import the `redg` project into your IDE (when using IDEA, open in new window)
 - Set your IDE up for remote debugging of the `redg` project
 - In your other project, add the remote debugging VM options to your Maven profile (`-agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=5005`)
 - Start the Maven build in the other project
 - Set your breakpoints
 - Attach the remote debugger