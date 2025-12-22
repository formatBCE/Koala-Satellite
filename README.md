
# LumiTone S1
<img width="768" height="581" alt="image" src="https://github.com/user-attachments/assets/2a517aef-6520-46d3-9f8c-11941c3efbdd" />

**A DIY Voice Satellite for Home Assistant, based on the ReSpeaker Lite.**

The **LumiTone S1** is a high-fidelity voice assistant satellite designed to integrate seamlessly into Home Assistant. This project is a customized iteration of the original [Koala Satellite](https://github.com/formatBCE/Koala-Satellite) by **FormatBCE**, combining their robust software foundation with a modified hardware layout and a completely new housing design.

Designed to match a modern interior, this version features a simplified "plug-and-play" interface (USB-C only) and an enhanced LED array for richer visual feedback.

> [!NOTE]
> **Credits & Acknowledgements**
> A massive shoutout to **[FormatBCE](https://github.com/formatBCE)** for developing the original code and the Koala Satellite concept. Their work on the ReSpeaker Lite ESPHome integration made this project possible. Please check out the original repository here: [https://github.com/formatBCE/Koala-Satellite](https://github.com/formatBCE/Koala-Satellite)

## 🛠️ Modifications in this Version
While based on the original Koala, this build ("LumiTone S1") introduces the following changes:
* **Simplified I/O:** The USR button has been removed for a cleaner look; the device relies solely on USB-C.
* **Enhanced Lighting:** Added a high-density strip of **20 WS2812 LEDs** connected to **Pin D2 (GPIO3)** for status and mood lighting.
* **Custom Enclosure:** A redesigned casing with a magnetic, removable front cover for easy customization and maintenance.

## ✨ Main Characteristics
* **🎙️ Voice Capture:** Two far-field microphones coupled with the **XMOS XU-316** chip for superior noise cancellation and voice recognition, and using 'ok Lumi' to wake it!
* **🔊 Audio:** High-quality 16-bit 48kHz audio output driving a **5W, 8-ohm dual speaker** setup.
* **💡 Visuals:** Integrated WS2812 LED strip with new "listening", "processing", "speaking" and optional "idle" status effects.
* **🔌 Connectivity:** Powered and controlled via a single USB-C connection using ESPHome.

### Real Life images
<img width="636" height="479" alt="image" src="https://github.com/user-attachments/assets/0e0f4e27-b414-4a6e-9cdf-bcd2c22182f6" />
<img width="636" height="479" alt="image" src="https://github.com/user-attachments/assets/25f07c50-68db-448b-9fa4-8434a7cb0871" />


https://github.com/user-attachments/assets/3ea72efb-40e6-4190-b0d5-7ef0238ab249


