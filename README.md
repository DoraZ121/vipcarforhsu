
![嘉賓大小章](https://github.com/user-attachments/assets/57033310-6076-4c26-859d-dbe952897377)
<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8" />
  <title>派車單填寫與匯出</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js" defer></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js" defer></script>
  <style>
    body { font-family: "Microsoft JhengHei", sans-serif; margin: 20px; display: flex; gap: 20px; }
    .form-section { width: 40%; }
    .preview-section { width: 60%; border: 1px solid #ccc; padding: 10px; background: #ffffff; }
    .dispatch-form { width: 100%; border-collapse: collapse; font-size: 14px; }
    .dispatch-form td, .dispatch-form th { border: 1px solid #000; padding: 4px; text-align: center; }
    .stamp { width: 80px; }
    .stamp-box { display: flex; justify-content: space-between; padding: 10px 0; }
  </style>
</head>
<body>
  <div class="form-section">
    <h3>派車單填寫</h3>
    <label>日期：<input type="date" id="dateInput" data-preview="p-date" /></label><br /><br />
    <label>客戶名稱：<input type="text" id="clientInput" data-preview="p-client" /></label><br /><br />
    <label>聯絡電話：<input type="tel" id="phoneInput" data-preview="p-phone" /></label><br /><br />
    <label>用車時間：<input type="text" id="timeInput" placeholder="例如：08:00 - 18:00" data-preview="p-time" /></label><br /><br />
    <label>碼表里程：<input type="text" id="mileageInput" data-preview="p-mileage" /></label><br /><br />
    <label>行程路線：<br /><textarea id="routeInput" rows="4" cols="30" data-preview="p-route"></textarea></label><br /><br />
    <button id="exportBtn">匯出為 PNG</button>
    <button id="pdfBtn">匯出為 PDF</button>
  </div>

  <div class="preview-section" id="capture">
    <h3 style="text-align: center">嘉賓小客車租賃有限公司 派車簽證單</h3>
    <p>地址：台北市松山區民權東路三段152號　電話：(02)2713-1111　傳真：(02)2713-0000</p>
    <table class="dispatch-form">
      <tr><td>日期</td><td colspan="3" id="p-date"></td></tr>
      <tr><td>客戶名稱</td><td id="p-client"></td><td>聯絡電話</td><td id="p-phone"></td></tr>
      <tr><td>車號</td><td>RDD-9153</td><td>駕駛人</td><td>徐文柏</td></tr>
      <tr><td>駕照號碼</td><td colspan="3">A127836925</td></tr>
      <tr><td>用車時間</td><td colspan="3" id="p-time"></td></tr>
      <tr><td>碼表里程</td><td colspan="3" id="p-mileage"></td></tr>
      <tr><td>行程路線</td><td colspan="3" id="p-route" style="height: 80px; text-align: left"></td></tr>
    </table>
    <div class="stamp-box">
      <img class="stamp" src="./嘉賓大小章.png" alt="章圖" />
      <img class="stamp" src="./嘉賓大小章.png" alt="章圖" style="margin-left: auto" />
    </div>
  </div>

  <script defer>
    const fieldMap = [
      { input: "dateInput", preview: "p-date", defaultToday: true },
      { input: "clientInput", preview: "p-client" },
      { input: "phoneInput", preview: "p-phone" },
      { input: "timeInput", preview: "p-time" },
      { input: "mileageInput", preview: "p-mileage" },
      { input: "routeInput", preview: "p-route", multiline: true },
    ];
    function updatePreview(inputEl, previewEl) {
      if (!inputEl || !previewEl) return;
      const value = inputEl.value;
      if (inputEl.tagName === "TEXTAREA") {
        previewEl.innerHTML = value.replace(/\n/g, "<br>");
      } else {
        previewEl.textContent = value;
      }
    }
    function autoFillToday(inputEl, previewEl) {
      const todayStr = new Date().toISOString().split("T")[0];
      inputEl.value = todayStr;
      previewEl.textContent = todayStr;
    }
    window.addEventListener("DOMContentLoaded", () => {
      fieldMap.forEach(({ input, preview, defaultToday }) => {
        const inputEl = document.getElementById(input);
        const previewEl = document.getElementById(preview);
        if (!inputEl || !previewEl) return;
        if (defaultToday && !inputEl.value) autoFillToday(inputEl, previewEl);
        inputEl.addEventListener("input", () => updatePreview(inputEl, previewEl));
      });

      document.getElementById("exportBtn").addEventListener("click", handleExportPNG);
      document.getElementById("pdfBtn").addEventListener("click", handleExportPDF);
    });

    function handleExportPNG() {
      html2canvas(document.getElementById("capture"), { scale: 2 }).then((canvas) => {
        const link = document.createElement("a");
        link.download = "派車單.png";
        link.href = canvas.toDataURL("image/png");
        link.click();
      });
    }

    function handleExportPDF() {
      html2canvas(document.getElementById("capture"), { scale: 2 }).then((canvas) => {
        const imgData = canvas.toDataURL("image/png");
        const { jsPDF } = window.jspdf;
        const pdf = new jsPDF("p", "mm", "a4");
        const pdfWidth = pdf.internal.pageSize.getWidth();
        const pdfHeight = (canvas.height * pdfWidth) / canvas.width;
        pdf.addImage(imgData, "PNG", 0, 0, pdfWidth, pdfHeight);
        pdf.save("派車單.pdf");
      });
    }
  </script>
</body>
</html>
