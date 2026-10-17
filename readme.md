# singlemon - AI CODED

Automatically routes a Windows PC's keyboard and mouse to an Amiga when the shared monitor displays the Amiga HDMI input. Switching the monitor back restores local Windows control. The Amiga receiver uses standard `input.device`.

## 1. AmigaOS setup

1. Start MiamiDX, Roadshow, or another stack supporting `bsdsocket.library`.
2. Make sure the Amiga has an IP address reachable from Windows.
3. Copy `l3-netkeys` to a convenient directory and start it in an Amiga Shell:

   ```text
   l3-netkeys
   ```

   This listens on UDP port **6800**. For a different port: `l3-netkeys 7000` (match it with `--port 7000` on Windows).
   
5. Leave the receiver running. Stop it with **Ctrl+C** in its Shell when finished.

## 2. Windows setup

1. Copy `SINGLEMON.exe` to a permanent directory, for example `C:\Tools\`.
2. Confirm .NET Framework 4.x is available and DDC/CI is enabled in the monitor's on-screen menu.
3. Create a shortcut to `SINGLEMON.exe`.
4. Open **Shortcut → Properties → Target** and enter (replace the IP and HDMI input as needed):

   ```text
   "C:\Tools\HDMIKVM\HDMIKVM.exe" --host 192.168.1.29 --target 17 --windows-source auto --capture
   ```

5. Optionally set **Run: Minimized** and place the shortcut on the desktop or Start menu.

The source selected at launch is detected as the **Windows** input; `--target 17` identifies the **Amiga** input. The program does **not** switch the monitor itself.

## 3. Everyday use

1. Start the Amiga network stack and `l3-netkeys`.
2. Select the **Windows** monitor input and launch the shortcut.
3. Switch the monitor to the **Amiga** input. After confirmation, PC keyboard and mouse control AmigaOS; normal Windows desktop input is suppressed.
4. Switch back to the **Windows** input to restore local control.
5. Close SINGLEMON after returning to the Windows input.

**Emergency release:** **Ctrl+Alt+Shift+F12** restores local Windows input. Low-level Windows hooks are not complete HID isolation; use on an attended desktop.

## 4. Useful commands and options

```bat
SINGLEMON.exe --list
SINGLEMON.exe --host 192.168.1.29 --target 17 --windows-source auto --capture
SINGLEMON.exe --host 192.168.1.29 --target 17 --windows-source auto --capture --mouse-scale 0.5
SINGLEMON.exe --host 192.168.1.29 --target 17 --windows-source auto --capture --interval 2000
```

| Option | Purpose |
|---|---|
| `--host IP` | Amiga IPv4 destination (required for forwarding) |
| `--target N` | Amiga monitor input (e.g. `17` or `0x11`) |
| `--windows-source auto` | Detect Windows input at startup |
| `--capture` | Enable capture and forwarding; omit for observation only |
| `--interval N` | DDC/CI polling, **250–2000 ms**; default **1000** |
| `--monitor N` | Choose the monitor; default `0` |
| `--port N` | UDP destination port; default `6800` |
| `--mouse-scale N` | Relative mouse sensitivity; values below `1` are supported |
| `--wheel-steps N` | Amiga cursor-key pulses per wheel notch |
| `--selftest-keys` | Check keyboard mapping |

### Special keys

| PC keyboard | Amiga |
|---|---|
| Left Windows | Left Amiga (`0x66`) |
| Application/Menu | Right Amiga (`0x67`) |
| Right Windows | Right Amiga (`0x67`) |
| Left Alt / Right Alt | Left Alt / Right Alt |
| Left Shift / Right Shift | Left Shift / Right Shift |

## 5. Important limitations

- DDC/CI source reading depends on the monitor; SINGLEMON was validated on the development setup, not every model.
- Windows low-level hooks cannot guarantee absolute suppression of system/secure-desktop or independently captured HID input.
- SINGLEMON uses **unauthenticated UDP**, use only on a **trusted/isolated network**; do not expose UDP 6800 to the Internet.
