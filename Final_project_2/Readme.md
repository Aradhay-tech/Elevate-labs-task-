STEGRANOGRAPHY TOOL (HIDE TEXT IN IMAGES)
By Aradhay Kopulwar

---

## PROJECT OVERVIEW

This project is about steganography — the process of hiding secret messages inside digital images in such a way that they are not visible to the human eye.

The tool allows the user to:

1. Hide a secret text message inside an image.
2. Extract the hidden message back from the image.
3. Optionally protect the message using a simple password encryption method.

The project uses Python libraries like Pillow and NumPy. It is designed to run easily on Google Colab.

---

## OBJECTIVE

To create a simple and effective Python tool that hides text messages inside images using the Least Significant Bit (LSB) technique.

---

## TOOLS AND LIBRARIES USED

1. Python
2. Pillow (PIL) - for image processing
3. NumPy - for pixel-level manipulation
4. Google Colab (files module) - for uploading and downloading images

To install required libraries:
pip install pillow numpy

---

## HOW IT WORKS

1. Each pixel of an image is made up of RGB values (red, green, blue).
2. The secret message is first converted into binary form.
3. These binary bits are hidden inside the least significant bits (LSB) of the image pixels.
4. When decoding, the program reads these bits and converts them back into readable text.
5. Optionally, the message can be encrypted using a simple XOR cipher with a password.

---

## CODE (FOR GOOGLE COLAB)

from PIL import Image
import numpy as np
from google.colab import files

def upload_image():
uploaded = files.upload()
image_path = list(uploaded.keys())[0]
print("Uploaded:", image_path)
return image_path

def encode_image(image_path, message, output_path):
img = Image.open(image_path)
np_img = np.array(img)

```
binary_message = ''.join(format(ord(c), '08b') for c in message)
binary_message += '1111111111111110'

data = np_img.flatten()
if len(binary_message) > len(data):
    raise ValueError("Message too long for this image!")

for i in range(len(binary_message)):
    data[i] = (data[i] & ~1) | int(binary_message[i])

encoded_img = data.reshape(np_img.shape)
Image.fromarray(encoded_img.astype(np.uint8)).save(output_path)
print("Message hidden successfully in", output_path)
```

def decode_image(image_path):
img = Image.open(image_path)
np_img = np.array(img)
data = np_img.flatten()

```
bits = [str(data[i] & 1) for i in range(len(data))]
all_bits = ''.join(bits)
chars = [all_bits[i:i+8] for i in range(0, len(all_bits), 8)]
message = ''
for idx, c in enumerate(chars):
    if c == '11111111' and idx + 1 < len(chars) and chars[idx + 1] == '11111110':
        break
    message += chr(int(c, 2))
return message
```

def xor_encrypt_decrypt(message, key):
return ''.join(chr(ord(c) ^ ord(key[i % len(key)])) for i, c in enumerate(message))

print("1. Hide a message inside an image")
print("2. Extract a message from an image")
choice = input("Enter choice (1/2): ")

if choice == "1":
print("Upload the image to hide the message in:")
image_path = upload_image()
message = input("Enter your secret message: ")
encrypt_choice = input("Add password encryption? (y/n): ").lower()
if encrypt_choice == "y":
key = input("Enter password: ")
message = xor_encrypt_decrypt(message, key)
output_path = "encoded_image.png"
encode_image(image_path, message, output_path)
print("Download your encoded image below:")
files.download(output_path)

elif choice == "2":
print("Upload the encoded image:")
image_path = upload_image()
extracted_message = decode_image(image_path)
decrypt_choice = input("Was this message password protected? (y/n): ").lower()
if decrypt_choice == "y":
key = input("Enter password: ")
extracted_message = xor_encrypt_decrypt(extracted_message, key)
print("Hidden Message:", extracted_message)
else:
print("Invalid choice. Please restart the cell.")

---

 STEPS TO RUN (IN GOOGLE COLAB)

1. Open Google Colab.
2. Copy and paste the code into a new code cell.
3. Run the cell.
4. Choose:

   * 1 to hide a message.
   * 2 to extract a message.
5. Upload your image when asked.
6. Enter your message and optional password.
7. Download the encoded image.
8. Later, re-upload the encoded image to extract the message.

---

 EXAMPLE WORKFLOW

Step 1: Upload image "input.png"
Step 2: Enter secret message "Hello from Aradhay"
Step 3: Enter password "1234" (optional)
Step 4: Encoded image is downloaded as "encoded_image.png"
Step 5: Upload "encoded_image.png" again to decode
Step 6: Enter password if used
Output: "Hello from Aradhay"

---

 LEARNING OUTCOMES

1. Understand how digital images are represented using pixels.
2. Learn about Least Significant Bit (LSB) steganography.
3. Practice binary and bitwise manipulation.
4. Understand simple encryption and decryption using XOR.

---

FUTURE IMPROVEMENTS

1. Add a GUI using Tkinter or ipywidgets.
2. Add file hiding support (not just text).
3. Use stronger encryption such as AES.
4. Compare original and encoded images visually.

---

AUTHOR

Name: Aradhay Kopulwar
Role: Python Developer | Tech Enthusiast
Interest: Security, Image Processing, Data Science

---

LICENSE

This project is open-source and free for educational use.
