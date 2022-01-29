# gba-mod
This mod makes the [GameBoid](https://www.emuparadise.me/Nintendo_Gameboy_Advance_Emulators/Android/GameBoid/131) useable on phones without a menu button.

# General introduction into APK modding
1. Disassemble resources with [apktool](https://github.com/iBotPeaches/Apktool):
    + `java -jar apktool.jar d app.apk`
2. Mod/Debug app (with [smalidea](https://github.com/JesusFreke/smalidea) or any editor)
3. Assemble back with apktool:
    + `java -jar apktool.jar b app-dir/`
4. [Sign](https://github.com/appium/sign) modded apk:
    + `java -jar sign.jar app-dir/dist/app.apk`
    * Optional:
    + [convert](https://github.com/pxb1988/dex2jar) .dex to .class files
        - `./dex2jar-2.0/d2j-dex2jar.sh app.apk`
    + [decompile](https://www.benf.org/other/cfr/) .class to .java
        - `java -jar cfr_0_122.jar app-dex2jar.jar --outputdir out/`
		

# Mod GameBoid to be usable on android versions without menu button
1. Make the back button open up the options menu in the emulator view
  + Remove the back button from the default keymap in DefaultPreferences.smali

    + ``` 
        $ diff origin/com.androidemu.gba-65-2.4.7/smali/com/androidemu/gba/DefaultPreferences.smali mod/com.androidemu.gba-65-2.4.7/smali/com/androidemu/gba/DefaultPreferences.smali

        36,42d35
        <     const/4 v2, 0x7
        < 
        <     .line 30
        <     const/4 v3, 0x4
        < 
        <     aput v3, v1, v2
        ```

  + Call openOptionsMenu() instead of showDialog() in EmulatorActivity.smali

    + ``` 
        $ diff origin/com.androidemu.gba-65-2.4.7/smali/com/androidemu/gba/EmulatorActivity.smali mod/com.androidemu.gba-65-2.4.7/smali/com/androidemu/gba/EmulatorActivity.smali

        2567c2567
        <     invoke-virtual {p0, v1}, Lcom/androidemu/gba/EmulatorActivity;->showDialog(I)V
        ---
        >     invoke-virtual {p0}, Lcom/androidemu/gba/EmulatorActivity;->openOptionsMenu()V
        ```

2. Open the options menu in the cheat view on creation
    + add onAttachedToWindow() and call openOptionsMenu() in CheatsActivity.
    + ```
        $ diff origin/com.androidemu.gba-65-2.4.7/smali/com/androidemu/gba/CheatsActivity.smali mod/GameBoid-2.5/smali/com/androidemu/gba/CheatsActivity.smali

        334a335,343
        > .method public onAttachedToWindow()V
        >     .locals 0
        > 
        >     .prologue
        >     invoke-virtual {p0}, Lcom/androidemu/gba/EmulatorActivity;->openOptionsMenu()V
        > 
        >     return-void
        > .end method
        ```
        
## License
This Project is released under GNU GPLv3 licence. Copyright (c) Martin Muenning
