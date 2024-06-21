So, as I play Minecraft and already used Forge or Fabric to modify the game, I wondered how difficult it would be to
make my own app supporting plugins.
As it turns out, it's not that hard but also not that easy.

## What makes up a plugin system?

A simple plugin system consists of a few parts:

- The plugin interface: This is the interface that all plugins have to implement. It defines the methods that the
  plugin system can call.
- The plugin loader: This is the part that loads the plugins from the filesystem and initializes them.
- The plugins: These are the actual plugins that do the work.

In a more complex system you will have more than that.

## Plugin interface

A good plugin interface for understanding looks like this:

```kotlin
interface Plugin {
    val name: String
    val version: String // Better: use a Version class or similar
    fun onEnable(context: PluginContext)
    fun onDisable(context: PluginContext)
}
```

This interface defines a few things:

- `onEnable`: This method is called when the plugin should be enabled. This should happen after all plugin instances
  have been created and the plugin system is ready to go. This is important because plugins might depend on each other
  and for that they might need to perform version checks.
- `onDisable`: If the plugin should be disabled, this method is called. This is important for cleaning up resources and
  saving data.
- `name` and `version`: These are used to identify the plugin.

## Plugin loader

The plugin loader is responsible for loading the plugins from the filesystem and initializing them. Here I want to show
how it works by scanning a directory for `.jar`s and loading them.

I've done it that way:

```kotlin
class PluginsLoader {
    fun getPlugins(directory: String): List<Plugin> {
        val pluginsDir = File(directory)
        val plugins = mutableListOf<Plugin>()
        if (pluginsDir.isDirectory) {
            val files = pluginsDir.listFiles()
            val jars = files?.filter { it.extension == "jar" } ?: emptyList<File>()
            for (file in jars) {
                val cl = URLClassLoader(arrayOf(file.toURI().toURL()), ClassLoader.getSystemClassLoader())
                val jarInputStream = JarInputStream(FileInputStream(file))
                val manifest = jarInputStream.manifest!!
                val pluginClassName = manifest.mainAttributes!!.getValue("Plugin-Name") ?: break
                val classToLoad = Class.forName(pluginClassName, true, cl)
                plugins += classToLoad.getDeclaredConstructor().newInstance() as Plugin
            }
        }
        return plugins
    }
}
```

The `getPlugins` function does the following things:

- It scans the `directory` for `.jar` files.
- For every `.jar` file it creates a new `URLClassLoader` and reads the `Plugin-Name` attribute from the manifest. Then
  the plugin - if loaded successfully - is added to the list of plugins.

### Why it is a good idea to use manifest for the plugin class

This gives the developers of the plugin the ability to define the name of the main class of the plugin. Without this, we
would have to set a fixed name like `Main` or `Plugin` or use like the file name. So we just use the more flexible way.

## The Plugin
For differentiation: I will use _App_ for the Application that uses the plugin system and _Plugin_ for the plugin.


To develop the plugin you first have  to create a new project and use your build App as a library. Then we also need to
add the manifest entry `Plugin-Name` to the `build.gradle.kts` file:

```groovy
tasks.withType<Jar> {
    manifest {
        attributes(
            "Plugin-Name" to "MyExamplePlugin"
        )
    }
}
```

If you then build the Plugin, you can put the `.jar` file into the directory that the `PluginsLoader` scans.

Congratulations 🎉! You have now created a simple plugin system. You can now extend it with more features like version checks,
dependencies, and more.