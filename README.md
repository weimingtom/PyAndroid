# PyAndroid

A proof-of-concept of using [Jython](http://jython.org/) to integrate powerful Python scripting (interacting directly with Java packages, objects, etc.) inside an Android application.

## Notes

* Jython operates by producing raw Java bytecode (class files). As noted briefly in *MainActivity.java*, this causes issues for Android, which does not use class files but Dalvik dex files. Thankfully, the Android SDK library for producing dex files, *dx.jar* (found on my system at *build-tools/23.0.2/lib/dx.jar*), runs on Android, but some tweaks are required to have it accept raw Java bytecode. (See *Dexer.java*.) To incorporate this, I removed the *org/python/core/BytecodeLoader.class* file from the Jython 2.7.0 distribution JAR and included a modified implementation in the source tree.
* Jython 2.5.4 was the last version to support Java 6, which Android is based on. When using the *os* library, newer versions (on Android and other Unix-like systems) require the *java.nio.file* library, which is not available on Android. However, the libraries used in Jython 2.5.4 are funky, and `dx` doesn't like them. To overcome this, I removed the *org/python/modules/posix* directory from the Jython 2.7.0 distribution JAR and included the 2.5.4 implementation in the source tree.
* The hard-coded entry-point of scripts is the */sdcard/PyAndroid/main.py* file.

## Example

    from android.util import Log
    Log.d("PyAndroid", "Hello from Python!")
    
    # Toasts must be launched from the main thread
    from java.lang import Runnable
    class Cheers(Runnable):
    	def run(self):
    		from android.widget import Toast
    		Toast.makeText(__service__, "Here's to you!", Toast.LENGTH_LONG).show()
    __service__.getHandler().post(Cheers())
    
    # Thank god for helper functions
    __service__.showToast("Champagne for my real friends and real pain for my sham friends!");
