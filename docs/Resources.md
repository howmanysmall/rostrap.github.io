# Resources

[Resources](https://github.com/RoStrap/Resources/blob/master/Resources.module.lua) is the core resource manager and library loader for RoStrap. It is designed to streamline the retrieval and networking of resources.

## Functionality
Upon being [required](http://wiki.roblox.com/index.php?title=Global_namespace/Roblox_namespace#require) on the server for the first time, [Resources](https://github.com/RoStrap/Resources/blob/master/Resources.module.lua) moves libraries within `ServerStorage.Repository` to folder `ReplicatedStorage.Resources.Libraries`, where both the client and server can [require](http://wiki.roblox.com/index.php?title=Global_namespace/Roblox_namespace#require) them via the function `LoadLibrary`.

![](https://image.prntscr.com/image/ZonjgCDFQLabru0xbMBUNQ.png)

Libraries with "Server" (case-sensitive) in their name or in the name of a parent folder are considered server-only libraries, and will not be moved to `ReplicatedStorage` with the rest of the libraries. Instead, these libraries will be moved to folder `ServerStorage.Resources`.

!!! info
	Internally, `LoadLibrary` caches and returns `require(Resources:GetLibrary(LibraryName))`. `GetLibrary` is the only function that can "retrieve" objects from both `ServerStorage` and `ReplicatedStorage`. This is because libraries are added directly to its cache.

!!! note
	Libraries will not be moved into `ReplicatedStorage` in Play-Solo (to allow for editing libraries during Play-Solo).


## API
All methods of [Resources](https://github.com/RoStrap/Resources/blob/master/Resources.module.lua) have hybrid syntax, meaning they can be called using either a `:` or `.`

### LoadLibrary

!!! summary

	A require-by-string function which internally calls `require(Resources:GetLibrary(string LibraryName))` and caches the results.

	<pre><a href="http://wiki.roblox.com/index.php?title=API:Variant" title="API:Variant"><span style="color:blue;">Variant</span></a> Resources:LoadLibrary (
		<a href="http://wiki.roblox.com/index.php?title=API:Type/string" class="mw-redirect" title="API:Type/string"><span>string</span></a> LibraryName
	)
	</pre>

!!! example
	```lua
	local SortedArray = Resources:LoadLibrary("SortedArray")

	-- Some people like to overwrite the default require function
	local require = Resources.LoadLibrary

	local Signal = require("Signal")
	```

!!! info
	This is the only function with a hard-coded name for its cache, so you may access cached require results using the following:

	```lua
	-- Resources uses this table to cache LoadLibrary results
	local LoadedLibraries = Resources:GetLocalTable("LoadedLibraries")
	```

### GetLocalTable

!!! summary

	A function which caches a local table by string, and returns said table. Does not replicate. Useful for eliminating the need for [_G](http://wiki.roblox.com/index.php?title=Global_namespace/Basic_functions#G) or ModuleScripts which return an empty table.

	<pre><a href="http://wiki.roblox.com/index.php?title=Table" title="Table"><span style="color:blue;">Table</span></a> Resources:GetLocalTable (
		<a href="http://wiki.roblox.com/index.php?title=API:Type/string" class="mw-redirect" title="API:Type/string"><span>string</span></a> TableName
	)
	</pre>

!!! example
	```lua
	-- Can be used in any script on the same machine to get this table
	local MyItems = Resources:GetLocalTable("MyItems") -- {}
	```

!!! info
	Resources uses `GetLocalTable` internally, so you can access its internals using this function:

	```lua
	-- Resources uses this table to cache LoadLibrary results
	local LoadedLibraries = Resources:GetLocalTable("LoadedLibraries")
	```
### Get functions

!!! summary
	Get functions can be procedurally generated by [Resources](https://github.com/RoStrap/Resources/blob/master/Resources.module.lua) at run-time in the form of `Resources:GetCLASSNAME(string Name)`. These functions return an [Instance](http://wiki.roblox.com/index.php?title=API:Class/Instance) of `Name` under `Resources:GetFolder(CLASSNAME:gsub("y$", "ie") .. "s")`. On the server, missing instances will be instantiated via [Instance.new](http://wiki.roblox.com/index.php?title=Instance_(Data_Structure)). On the client, the function will yield for the missing instances via [WaitForChild](http://wiki.roblox.com/index.php?title=API:Class/Instance/WaitForChild).

	<pre><a href="/index.php?title=API:Ref" title="API:Ref"><span style="color:blue;">Ref</span></a>&lt;<a href="/index.php?title=API:Class/Instance" title="API:Class/Instance"><span style="color:blue;">Instance</span></a>&gt; Resources:GetCLASSNAME (
		<a href="/index.php?title=API:Type/string" class="mw-redirect" title="API:Type/string"><span>string</span></a> InstanceName
	)</pre>

The following is the internal call tree. Keep in mind that these functions cache so the 3rd function will only run once per machine and `GetFolder` will only run once per function generation (e.g. the first time `GetRemoteEvent` is called).

|Step|Object to search for|Function call|
|:-:|:-:|:-:|
|1|RemoteEvent `Chatted` inside|GetRemoteEvent("Chatted")|
|2|Folder `RemoteEvents` in|GetFolder("RemoteEvents")|
|3|`Resources`|ROOT|

![](https://user-images.githubusercontent.com/15217173/38775951-d6bfbeee-404b-11e8-8396-9666a0b20b98.png)

!!! info
	* Caches results in `Resources:GetLocalTable(CLASSNAME:gsub("y$", "ie") .. "s")`

!!! example
	Get functions can also manage instance types that aren't instantiable by `Instance.new`. However, these instances must be preinstalled in the locations in which they would otherwise be instantiated because they cannot be generated at run-time. This allows you to do things like the following:

	```lua
	local Falchion = Resources:GetSword("Falchion")
	```

	![](https://user-images.githubusercontent.com/15217173/38775984-64af0b2e-404c-11e8-9279-0adace656665.png)

#### GetLocal functions

!!! summary
	Get functions in the form of `GetLocalCLASSNAME` work in the same way as regular Get functions, except clients may also instantiate missing instances and `GetLocalFolder` searches in different locations. Not meant to be used very much.

|**Machine**|LOCALSTORAGE|LOCALRESOURCES|
|:-----:|:----:|:----:|
|**Server**|`ServerStorage`|`ServerStorage.Resources`|
|**Client**|`Players.LocalPlayer.PlayerScripts`|`Players.LocalPlayer.PlayerScripts.Resources`|

|Step|Object to search for|Function call|
|:-:|:-:|:-:|
|1|BindableEvent `Attacking` inside|GetLocalBindableEvent("Attacking")|
|2|Folder `BindableEvents` in|GetLocalFolder("BindableEvents")|
|3|Folder `LOCALSTORAGE.Resources`|LOCALRESOURCES|

!!! example
	```lua
	local Attacking = Resources:GetLocalBindableEvent("Attacking")
	```

	Here is what the above code would do if ran by the server versus what it would do if ran by a client:

	|Server|Client|
	|:----:|:----:|
	|![](https://user-images.githubusercontent.com/15217173/38918583-6a9dcf50-42ab-11e8-8dbd-a165595af63f.png)|![](https://user-images.githubusercontent.com/15217173/38918817-00857d1a-42ac-11e8-9a3e-3176a2cb65b0.png)|


!!! warning
	In Play-Solo Mode, all local objects will go under `ServerStorage`, as there is no difference between the client and server. If you use identical Local-function calls on the client and server, this could cause conflicts in Play-Solo. The same problem applies to caches, which have "Local" appended to the front of the string to differentiate from regular caches. `LOCALSTORAGE` is typically just for `GetLocalBindableEvent` calls and having a place to store server-only Libraries, which are under `GetLocalFolder("Resources")`.
