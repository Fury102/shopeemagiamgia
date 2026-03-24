<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Tạo Link Affiliate Shopee</title>
<script src="https://cdn.tailwindcss.com"></script>
</head>

<body class="min-h-screen bg-gray-50 flex justify-center pt-12 p-4 font-sans">

<div class="max-w-2xl w-full bg-white rounded-2xl shadow-xl overflow-hidden">

<div class="bg-[#ee4d2d] p-6 text-white text-center">
<h1 class="text-xl md:text-2xl font-bold">
Dán Link Sản Phẩm Cần Mua
</h1>
</div>

<div class="p-6 space-y-6">

<div>
<label class="block text-sm font-semibold text-gray-700 mb-2">
Dán link Shopee vào
</label>

<div class="flex gap-2">
<input
id="inputUrl"
type="url"
class="flex-1 px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-[#ee4d2d] outline-none"
placeholder="https://s.shopee.vn/..."
/>

<button
onclick="pasteFromClipboard()"
class="px-4 py-3 bg-gray-200 rounded-lg hover:bg-gray-300 transition font-semibold"
>
Dán
</button>
</div>
</div>

<div id="errorBox" class="hidden text-red-600 bg-red-50 p-4 rounded-lg text-sm"></div>

<button
onclick="processUrl()"
id="mainBtn"
class="w-full py-4 rounded-lg text-white font-bold text-lg bg-[#ee4d2d] hover:bg-[#d74325] transition shadow-lg"
>
Bắt Đầu Tạo
</button>

<div id="resultBox" class="hidden mt-8 pt-6 border-t border-gray-100">

<label class="block text-sm font-semibold text-green-600 mb-2">
Copy rồi bình luận link này vào bài viết bất kỳ trên facebook rồi mở mua theo link vừa bình luận:
</label>

<div class="flex gap-3">

<!-- FACEBOOK BUTTON -->
<button
onclick="openFacebook()"
class="flex-1 flex items-center justify-center gap-2 px-4 py-3 rounded-lg font-semibold text-white bg-[#1877F2] hover:bg-[#166FE5] transition"
>
CMT trên FB
</button>

<!-- COPY BUTTON -->
<button
onclick="copyToClipboard()"
id="copyBtn"
class="flex-1 flex items-center justify-center gap-2 px-4 py-3 rounded-lg font-semibold border border-[#1877F2] text-[#1877F2] hover:bg-blue-50 transition"
>
Copy Link
</button>

</div>
</div>
</div>
</div>

<!-- TOAST -->
<div id="toast" class="fixed top-5 left-1/2 -translate-x-1/2 hidden"></div>

<script>

const affId = "17318400278";
const workerBase = "https://resolve-link.binhday102.workers.dev/?url=";

let generatedLink = "";

// Toast
function showToast(message, color="bg-black") {

const toast = document.getElementById("toast");

toast.innerText = message;

toast.className =
`fixed top-5 left-1/2 -translate-x-1/2 ${color}
 text-white px-6 py-3 rounded-lg shadow-lg
 transition-all duration-300 opacity-0`;

toast.classList.remove("hidden");

setTimeout(() => {
toast.classList.remove("opacity-0");
}, 10);

setTimeout(() => {
toast.classList.add("opacity-0");
setTimeout(() => {
toast.classList.add("hidden");
}, 300);
}, 2500);
}

// Paste
async function pasteFromClipboard() {
try {
const text = await navigator.clipboard.readText();
document.getElementById("inputUrl").value = text;
showToast("Đã dán từ clipboard", "bg-blue-600");
} catch {
showToast("Không thể dán. Hãy cho phép quyền clipboard.", "bg-red-600");
}
}

// Resolve
async function resolveShortLink(url) {
const workerURL = workerBase + encodeURIComponent(url);
const response = await fetch(workerURL);
const data = await response.json();
if (!data.success) throw new Error("Không thể resolve link.");
return data.resolved;
}

// Extract V1
function extractProductInfo(url) {
const regex = /(\d+)\/(\d+)/;
const match = url.match(regex);
if (!match) return null;
return { shopId: match[1], itemId: match[2] };
}

// Process
async function processUrl() {

const input = document.getElementById("inputUrl").value.trim();
const errorBox = document.getElementById("errorBox");
const resultBox = document.getElementById("resultBox");
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
if (input.includes("s.shopee.vn") || input.includes("shp.ee")) {
targetLink = await resolveShortLink(input);
}
} catch (err) {
errorBox.innerText = err.message;
errorBox.classList.remove("hidden");
btn.innerText = "Bắt Đầu Tạo";
btn.disabled = false;
return;
}

const productInfo = extractProductInfo(targetLink);

if (!productInfo) {
errorBox.innerText = "Không tìm thấy SHOPID và ITEMID.";
errorBox.classList.remove("hidden");
btn.innerText = "Bắt Đầu Tạo";
btn.disabled = false;
return;
}

const cleanProductUrl =
`https://shopee.vn/product/${productInfo.shopId}/${productInfo.itemId}`;

const encoded = encodeURIComponent(cleanProductUrl);

generatedLink =
`https://s.shopee.vn/an_redir?origin_link=${encoded}&affiliate_id=${affId}&sub_id=facebook`;

resultBox.classList.remove("hidden");

showToast("Tạo link thành công!", "bg-green-600");

btn.innerText = "Bắt Đầu Tạo";
btn.disabled = false;
}

// Copy
function copyToClipboard() {

if (!generatedLink) return;

navigator.clipboard.writeText(generatedLink).then(() => {
showToast("Đã copy link thành công!", "bg-green-600");
});
}

// Open Facebook
function openFacebook() {

if (!generatedLink) {
showToast("Vui lòng tạo link trước.", "bg-red-600");
return;
}

const appLink = "fb://facewebmodal/f?href=https://www.facebook.com/share/g/1DDF79v3b3/";
const webLink = "https://www.facebook.com/share/g/1DDF79v3b3/";

window.location.href = appLink;

setTimeout(() => {
window.open(webLink, "_blank");
}, 500);
}

</script>

</body>
</html>
