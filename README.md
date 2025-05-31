<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Pengumuman Nilai Asli UJIAN SEMESTER GENAP TP. 2024/2025 - TKJ SMKN 1 BUAY BAHUGA</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">
  <div class="bg-white shadow-lg rounded-xl p-6 w-full max-w-md">
    <h2 class="text-2xl font-bold text-center text-blue-700 mb-6">Pengumuman Nilai Asli UJIAN SEMESTER GENAP TP. 2024/2025<br>Teknologi Layanan Jaringan<br>TKJ SMKN 1 BUAY BAHUGA</h2>

    <input
      id="namaInput"
      type="text"
      placeholder="Masukkan Nama Lengkap (misal: Abdul Mu'ti)"
      class="w-full border border-gray-300 rounded-lg p-3 mb-4 focus:outline-none focus:ring-2 focus:ring-blue-500"
    />

    <input
      id="kelasInput"
      type="text"
      placeholder="Masukkan Kelas (misal: 11 TKJ 2)"
      class="w-full border border-gray-300 rounded-lg p-3 mb-4 focus:outline-none focus:ring-2 focus:ring-blue-500"
    />

    <button
      onclick="cekKelulusan()"
      class="w-full bg-blue-600 text-white py-3 rounded-lg hover:bg-blue-700"
    >
      Cek Kelulusan
    </button>

    <div id="hasil" class="hidden mt-6 text-center border-t pt-4">
      <p><strong>Nama:</strong> <span id="nama"></span></p>
      <p><strong>Kelas:</strong> <span id="kelas"></span></p>
      <p><strong>Nilai:</strong> <span id="nilai"></span></p>
      <p><strong>Status:</strong> <span id="keterangan" class="font-bold"></span></p>

      <button
        onclick="cetakPDF()"
        class="mt-4 bg-green-600 text-white py-2 px-4 rounded-lg hover:bg-green-700"
      >
        Cetak Surat Kelulusan (PDF)
      </button>
    </div>

    <p id="error" class="mt-4 text-red-500 text-center hidden"></p>
  </div>

  <div
    id="pdfContent"
    class="hidden p-8 text-black bg-white max-w-md mx-auto text-center border border-gray-300 rounded-lg"
  >
    <h2 class="text-xl font-bold mb-4">SURAT KETERANGAN HASIL NILAI UAS GENAP TP. 2024/2025</h2>
    <p>Dengan ini menyatakan bahwa:</p>
    <p class="mt-4"><strong>Nama:</strong> <span id="pdfNama"></span></p>
    <p><strong>Kelas:</strong> <span id="pdfKelas"></span></p>
    <p><strong>Nilai:</strong> <span id="pdfNilai"></span></p>
    <p><strong>Keterangan:</strong> <span id="pdfStatus" class="font-bold"></span></p>
    <p class="mt-6">
      Telah dinyatakan <strong id="pdfStatusText" class="uppercase"></strong> berdasarkan hasil evaluasi akademik.
    </p>
    <p class="mt-6">
      SMKN 1 BUAY BAHUGA<br /><small>Tanggal: <span id="tanggalCetak"></span></small>
    </p>
  </div>

  <script>
    let dataKelulusan = null;

    async function cekKelulusan() {
      const nama = document.getElementById("namaInput").value.trim().toLowerCase();
      const kelas = document.getElementById("kelasInput").value.trim().toLowerCase();
      const hasil = document.getElementById("hasil");
      const error = document.getElementById("error");

      hasil.classList.add("hidden");
      error.classList.add("hidden");

      if (!nama || !kelas) {
        error.textContent = "Silakan masukkan nama dan kelas Anda.";
        error.classList.remove("hidden");
        return;
      }

      try {
        const response = await fetch(
          `https://SCRIPT_ID_EXEC_URL_HERE?nama=${encodeURIComponent(nama)}&kelas=${encodeURIComponent(kelas)}`
        );
        const data = await response.json();

        if (data.error) {
          error.textContent = data.error;
          error.classList.remove("hidden");
        } else {
          dataKelulusan = data;
          document.getElementById("nama").textContent = data.nama;
          document.getElementById("kelas").textContent = data.kelas;
          document.getElementById("nilai").textContent = data.nilai;
          const status = document.getElementById("status");
          status.textContent = data.status;

          status.className = "font-bold " + (data.status === "LULUS" ? "text-green-600" : "text-red-600");
          hasil.classList.remove("hidden");
        }
      } catch (e) {
        error.textContent = "Terjadi kesalahan saat mengambil data.";
        error.classList.remove("hidden");
      }
    }

    function cetakPDF() {
      if (!dataKelulusan) return;

      document.getElementById("pdfNama").textContent = dataKelulusan.nama;
      document.getElementById("pdfKelas").textContent = dataKelulusan.kelas;
      document.getElementById("pdfNilai").textContent = dataKelulusan.nilai;
      document.getElementById("pdfStatus").textContent = dataKelulusan.status;
      document.getElementById("pdfStatusText").textContent = dataKelulusan.status;
      document.getElementById("tanggalCetak").textContent = new Date().toLocaleDateString("id-ID");

      const element = document.getElementById("pdfContent");
      html2pdf()
        .set({
          margin: 1,
          filename: `Surat_Kelulusan_${dataKelulusan.nama.replace(/\s+/g, "_")}.pdf`,
          image: { type: "jpeg", quality: 0.98 },
          html2canvas: { scale: 2 },
          jsPDF: { unit: "cm", format: "a4", orientation: "portrait" },
        })
        .from(element)
        .save();
    }
  </script>
</body>
</html>
