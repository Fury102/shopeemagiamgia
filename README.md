<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Tạo Link Affiliate Shopee</title>
<script src="https://cdn.tailwindcss.com"></script>
</head>

<body class="min-h-screen bg-gray-50 flex items-center justify-center p-4 font-sans">

<div class="max-w-2xl w-full bg-white rounded-2xl shadow-xl overflow-hidden">

<div class="bg-[#ee4d2d] p-6 text-white text-center">
<h1 class="text-xl md:text-2xl font-bold">
Tool Tạo Link Affiliate Shopee
</h1>
</div>

<div class="p-6 space-y-6">

<div>
<label class="block text-sm font-semibold text-gray-700 mb-2">
Dán link Shopee vào
</label>
<input
id="inputUrl"
type="url"
class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-[#ee4d2d] outline-none"
placeholder="https://s.shopee.vn/..."
/>
</div>

<div id="errorBox" class="hidden text-red-600 bg-red-50 p-4 rounded-lg text-sm"></div>

<button
onclick="processUrl()"
id="mainBtn"
class="w-full py-4 rounded-lg text-white font-bold text-lg bg-[#ee4d2d] hover:bg-[#d74325] transition shadow-lg"
>
Tạo Link Affiliate
</button>

<div id="resultBox" class="hidden mt-8 pt-6 border-t border-gray-100">
<label class="block text-sm font-semibold text-green-600 mb-2">
Link affiliate của bạn:
</label>

<div class="flex flex-col gap-3">
<textarea
id="outputUrl"
readonly
class="w-full h-28 p-3 border border-green-200 bg-green-50 rounded-lg text-gray-700 font-mono text-sm resize-none"
></textarea>

<button
onclick="copyToClipboard()"
id="copyBtn"
class="px-6 py-3 rounded-lg font-semibold bg-gray-100 text-gray-700 hover:bg-gray-200 transition"
>
Copy Link
</button>
</div>
</div>

</div>
</div>

<script>

const affId = "17318400278";
const workerBase = "https://old-shape-d3d6.binhday102.workers.dev/?url=";

// Resolve short link
async function resolveShortLink(url) {

const workerURL = workerBase + encodeURIComponent(url);

const response = await fetch(workerURL);
const data = await response.json();

if (!data.success) {
    throw new Error("Không thể resolve link.");
}

return data.resolved;
}

// Trích xuất SHOPID + ITEMID
function extractProductInfo(url) {

const regex = /(\d+)\/(\d+)/;
const match = url.match(regex);

if (!match) return null;

return {
    shopId: match[1],
    itemId: match[2]
};
}

async function processUrl() {

const input = document.getElementById("inputUrl").value.trim();
const errorBox = document.getElementById("errorBox");
const resultBox = document.getElementById("resultBox");
const output = document.getElementById("outputUrl");
const btn = document.getElementById("mainBtn");

errorBox.classList.add("hidden");
resultBox.classList.add("hidden");

if (!input) {
errorBox.innerText = "Vui lòng nhập link.";
errorBox.classList.remove("hidden");
return;
}

btn.innerText = "Đang xử lý...";
btn.disabled = true;

let targetLink = input;

try {

if (input.includes("s.shopee.vn")) {
    targetLink = await resolveShortLink(input);
}

} catch (err) {
errorBox.innerText = err.message;
errorBox.classList.remove("hidden");
btn.innerText = "Tạo Link Affiliate";
btn.disabled = false;
return;
}

// Trích ID
const productInfo = extractProductInfo(targetLink);

if (!productInfo) {
errorBox.innerText = "Không tìm thấy SHOPID và ITEMID.";
errorBox.classList.remove("hidden");
btn.innerText = "Tạo Link Affiliate";
btn.disabled = false;
return;
}

// Luôn build lại chuẩn /product/
const cleanProductUrl =
`https://shopee.vn/product/${productInfo.shopId}/${productInfo.itemId}`;

const encoded = encodeURIComponent(cleanProductUrl);

const finalLink =
`https://s.shopee.vn/an_redir?origin_link=${encoded}&affiliate_id=${affId}&sub_id=facebook`;

output.value = finalLink;
resultBox.classList.remove("hidden");

btn.innerText = "Tạo Link Affiliate";
btn.disabled = false;
}

function copyToClipboard() {

const output = document.getElementById("outputUrl");

if (!output.value) return;

navigator.clipboard.writeText(output.value).then(() => {

const btn = document.getElementById("copyBtn");
btn.innerText = "Đã Copy!";
btn.classList.remove("bg-gray-100");
btn.classList.add("bg-green-600", "text-white");

setTimeout(() => {
btn.innerText = "Copy Link";
btn.classList.remove("bg-green-600", "text-white");
btn.classList.add("bg-gray-100");
}, 2000);

});
}

</script>

</body>
</html>
