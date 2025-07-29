import requests
import csv

API_KEY = "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"  # <<== Buraya kendi API anahtarını yapıştır

headers = {
    "Authorization": f"Bearer {API_KEY}",
    "Content-Type": "application/json"
}

arama_kelimesi = input("🔍 Hangi kelimeye benzer arama terimleri üretmek istersin? ")

data = {
    "model": "mistralai/mistral-7b-instruct",
    "messages": [
        {"role": "system", "content": "Sen kısa ve etkili anahtar kelime önerisi yapan bir asistansın."},
        {"role": "user", "content": f"'{arama_kelimesi}' kelimesine benzer 20 kısa ve etkili arama kelimesi üret. Sadece kelimeleri madde madde listele."}
    ]
}

response = requests.post("https://openrouter.ai/api/v1/chat/completions", headers=headers, json=data)

metin = response.json()["choices"][0]["message"]["content"]
benzer_kelimeler = [satır.strip("-•–. ").lower() for satır in metin.split("\n") if satır.strip()]

csv_adi = f"openrouter_benzer_{arama_kelimesi.replace(' ', '_')}.csv"
with open(csv_adi, mode="w", newline="", encoding="utf-8") as dosya:
    writer = csv.writer(dosya)
    writer.writerow(["Anahtar Kelime", "Benzer Kelime"])
    for kelime in benzer_kelimeler:
        writer.writerow([arama_kelimesi, kelime])

print(f"✅ {len(benzer_kelimeler)} kelime '{csv_adi}' dosyasına kaydedildi.")
