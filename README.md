# HotkeyParser
Parses the Hotkey if you want to only grab the Key, Modifiers, or get the full key names of the Modifier Keys
### What it simply does
Returns an Object containing Properties obtained by parsing your Hotkey that was plugged into the Function (defaults to A_ThisHotkey if parameter is omitted)
Note: It does not confirm if the Hotkey is valid but it should work fine as long as you don't somehow plug in incorrect Hotkeys.
### Examples of use-cases
1. Parsing the Hotkey to see what the Modifier and the Key is.
```ahk
F1::{ ; Hotkey is F1
    HotkeyObj := Fn_HotkeyParser("<+>!F2") ; We will be parsing what is plugged into this function
    ContString := "
    (
        Hotkey = {1}
        Key = {2}
        ModifierSymbols = {3}
        ModifierKeyNamesArray = {4}
    )"
    ModifierNamesText := ""
    For Index, Value in HotkeyObj.ModifierKeyNamesArray {
        ModifierNamesText .= Value " "
    }
    FormattedText := Format(ContString, HotkeyObj.Hotkey, HotkeyObj.Key, HotkeyObj.ModifierSymbols, ModifierNamesText)
    MsgBox FormattedText
}
```
2. Wait for all modifiers to be released before continuing with the Hotkey's code
```ahk
^!a::{ ; Hotkey is Ctrl + Alt + A
    HotkeyObj := Fn_HotkeyParser()
    ToolTip "Waiting for Ctrl and Alt to be released"
    For Index, Value in HotkeyObj.ModifierKeyNamesArray {
        KeyWait Value
    }
    ToolTip "Modifiers have been released"
}
```
3. Create a temporary 1-time use Hotkey that triggers on release.
```ahk
+F1::{ ; Hotkey is Shift + F1
    Static HotkeyObj := Fn_HotkeyParser()
    ToolTip "Shift + F1 is pressed"
    Hotkey "*" HotkeyObj.Key " Up", Callback_HotkeyUp, "On" ; creates a 1-time use hotkey that triggers when F1 is released regardless of modifier keys involved

    Static Callback_HotkeyUp(HotKeyUpName){
        ToolTip "F1 is released"
        Hotkey(HotKeyUpName, "Off") ; disables this HotkeyUp
    }
}
```
4. Using a single hotkey function for multiple hotkeys that have different modifiers.
```ahk
1:: ; Hotkey is 1
~2:: ; Hotkey is 2
*$3:: ; Hotkey is 3
{ 
    ; this Hotkey Function code is attached to all 3 Hotkeys stacked above.
    HotkeyObj := Fn_HotkeyParser()
    Tooltip HotkeyObj.Key " is held"
    KeyWait HotkeyObj.Key
    Tooltip HotkeyObj.Key " is released"
}
```
