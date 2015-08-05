As you have hopefully seen, patching an experimental VS instance with a new version of the MIEngine is super easy - just hit F5. But after you think your changes are ready, you might want to patch the regular (non-experimental) VS instance. Doing this is pretty easy too --
* Build the configuration you want to run (Debug or Release)
* Run `bin\debug\drop\Install.cmd` (Replace 'Debug' with 'Release' if that is what you want to patch with that)

### Notes
* The script grabs the files to patch from the same directory as where the script is running from
* You can use the '/restore' argument to the script to restore the original files
* The script just replaces whatever version the MIEngine that the computer has installed with the new version. So this is great to patch a few computers for testing, but this isn’t a good answer for widely distributing changes. Please work with Microsoft if you want to do this.

