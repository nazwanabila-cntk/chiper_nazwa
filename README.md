# --- Caesar & Vigenere Cipher (untuk Google Colab) ---
from google.colab import files

# ---------- Caesar Cipher ----------
def caesar_cipher(text, shift, mode='encrypt'):
    result = ''
    if mode == 'decrypt':
        shift = -shift
    for char in text:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            result += chr((ord(char) - base + shift) % 26 + base)
        else:
            result += char
    return result

# ---------- Vigenère Cipher ----------
def vigenere_cipher(text, key, mode='encrypt'):
    result = ''
    key = key.upper()
    key_index = 0
    for char in text:
        if char.isalpha():
            k = ord(key[key_index % len(key)]) - ord('A')
            if mode == 'decrypt':
                k = -k
            base = ord('A') if char.isupper() else ord('a')
            result += chr((ord(char) - base + k) % 26 + base)
            key_index += 1
        else:
            result += char
    return result

# ---------- Deteksi mode otomatis ----------
def detect_mode(filename):
    name = filename.lower()
    if "decrypt" in name or "encrypted" in name:
        return 'decrypt'
    return 'encrypt'

# ---------- Fungsi utama (untuk Colab) ----------
def process_file(input_file, method='Caesar', mode='auto', shift_or_key='3'):
    # Deteksi mode otomatis
    if mode == 'auto':
        mode = detect_mode(input_file)
        print(f"🔍 Mode otomatis terdeteksi: {mode}")

    # Baca isi file input
    with open(input_file, 'r', encoding="utf-8") as f:
        text = f.read()

    # Proses sesuai metode
    if method == 'Caesar':
        if not shift_or_key.isdigit():
            raise ValueError("Shift harus berupa angka untuk Caesar Cipher!")
        shift = int(shift_or_key)
        result = caesar_cipher(text, shift, mode)
    elif method == 'Vigenère':
        if not shift_or_key.isalpha():
            raise ValueError("Kunci harus berupa huruf untuk Vigenère Cipher!")
        result = vigenere_cipher(text, shift_or_key, mode)
    else:
        raise ValueError("Metode tidak dikenal! Gunakan 'Caesar' atau 'Vigenère'.")

    # Tentukan nama output otomatis
    if mode == 'encrypt':
        output_file = input_file.replace('.txt', '_encrypted.txt')
    else:
        output_file = input_file.replace('.txt', '_decrypted.txt')

    # Simpan hasil
    with open(output_file, 'w', encoding='utf-8') as f:
        f.write(result)

    print(f"✅ Proses {mode} selesai. File tersimpan: {output_file}")
    return output_file


# --- Upload dan proses file di Google Colab ---
uploaded = files.upload()  # pilih file .txt dari komputer

input_filename = list(uploaded.keys())[0]
print(f"\n✅ File berhasil diupload: {input_filename}\n")

# 🔹 Contoh: Caesar cipher otomatis (shift=3)
out = process_file(input_filename, method='Caesar', mode='auto', shift_or_key='3')

# 🔹 Kalau mau Vigenère, ganti baris di atas dengan:
# out = process_file(input_filename, method='Vigenère', mode='auto', shift_or_key='LEMON')

# --- Download hasil ---
print(f"\n📂 File hasil siap diunduh: {out}")
files.download(out)
