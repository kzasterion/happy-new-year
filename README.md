<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
@import url('https://fonts.googleapis.com/css2?family=Patrick+Hand&display=swap');

:root {
    --pixel-red: #D9754D;
    --pixel-yellow: #FFD98E;
    --pixel-dark: #212121;
    --pixel-bg: #BF3321;
}

body {
    margin: 0;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    font-family: 'Patrick Hand', cursive;
    background-color: var(--pixel-bg);
    background-image: radial-gradient(var(--pixel-red) 2px, transparent 2px);
    background-size: 20px 20px;
    color: var(--pixel-yellow);
}

.pixel-box {
    background: white;
    border: 4px solid var(--pixel-dark);
    box-shadow: 4px 4px 0 var(--pixel-dark);
    padding: 25px;
    max-width: 500px;
    width: 85%;
    color: var(--pixel-dark);
}

#man-nhap-ten { text-align: center; }
#man-nhap-ten p { font-size: 2rem; }

input {
    font-family: inherit;
    padding: 12px;
    font-size: 1.3rem;
    border: 3px solid var(--pixel-dark);
    text-align: center;
    width: 260px;
}

.pixel-btn {
    font-family: inherit;
    background: var(--pixel-yellow);
    border: 3px solid var(--pixel-dark);
    padding: 8px 20px;
    font-size: 1.3rem;
    cursor: pointer;
    margin-top: 15px;
}

.line-to { font-size: 1.6rem; color: var(--pixel-red); display: block; }
.line-content { white-space: pre-line; margin: 15px 0; font-size: 1.2rem; }
.line-from { font-style: italic; font-size: 1.1rem; }

#man-noi-dung, #nut { display: none; text-align: center; }
#bao-loi { display: none; background: var(--pixel-red); color: #fff; padding: 10px; margin-top: 10px; }
</style>
</head>
<body>

<div id="man-nhap-ten">
    <p>NHẬP TÊN CỦA BẠN</p>
    <input id="o-nhap-ten" placeholder="Ví dụ: Toasty X">
    <br>
    <button class="pixel-btn" onclick="batDau()">XÁC NHẬN →</button>
    <div id="bao-loi">TÊN KHÔNG CÓ TRONG DANH SÁCH!</div>
</div>

<div id="man-noi-dung" class="pixel-box">
    <div id="text"></div>
</div>

<div id="nut">
    <button class="pixel-btn" onclick="prevTrang()">⟵</button>
    <button class="pixel-btn" onclick="nextTrang()">⟶</button>
    <button class="pixel-btn" onclick="quayVe()">⟲</button>
</div>

<script>
let cacTrang = [];
let index = 0;

/* ===== DATA ===== */
const LOI_CHUC = {
"toasty x": { to:"To: Toasty X", from:"Kz",
content:"Chúc Tọt Te năm mới nhiều sức khỏe. Bình an và đạt được những điều mình mong mới trong năm nay!\nChúc cho Tọt Te thật nhiều sức khỏe để tiếp tục con đường mình đã chọn.\nMong mọi thứ sẽ nhẹ nhàng hơn với Tọt Te."},

"phạm hải anh": { to:"To: Phạm Hải Anh", from:"Ka dé ( ˘ ³˘ )",
content:"Akemashite omedetooooo\n\nChúc Hải Anh năm mới có thêm nhiều ngày vui, sức khoẻ tốt, công việc thuận lợi và mọi thứ đều ổn định."},

"đỗ phương anh": { to:"To: Đỗ Phương Anh", from:"Kz",
content:"Happy New Year\n\nMong Phương Anh dịu dàng hơn với chính mình, có người để tin và có điều để hy vọng."},

"nguyễn kim ngân": { to:"To: Nguyễn Kim Ngân", from:"Ka Văn Dé ♡",
content:"Happy New Year\n\nChúc Ngân nhiều sức khỏe, niềm vui và mong mỗi ngày đều là ngày vui."},

"hoàng ngọc dương": { to:"To: Hoàng Ngọc Dương", from:"Gà Ủ Muối",
content:"Chúc Mừng Năm Mới\n\nChúc anh Dương an yên, khoẻ mạnh, ăn no ngủ kỹ và còn thời gian để em làm phiền đều đều =)))"},

"nguyễn đức minh quang": { to:"To: Nguyễn Đức Minh Quang", from:"con bé Kz",
content:"Happy New Yearrr\n\nChúc anh Quang năm mới khoẻ mạnh, tinh thần thoải mái và nhiều cơ hội trong công việc."},

"nguyễn anh nhật": { to:"To: Nguyễn Anh Nhật", from:"Quỷ Nhỏ",
content:"Chúc Mừng Năm Mới!\n\nChúc anh Nhật nhiều sức khoẻ, bình an và mọi điều tốt đẹp sẽ đến trong năm nay."},

"phan tiến thịnh": { to:"To: Phan Tiến Thịnh", from:"Hoàng Vũ Kiên Giang",
content:"Happy New Year!\n\nChúc chú Hữu nhiều niềm vui, sức khoẻ và cảm ơn chú vì luôn tử tế với cháu."},

"diệu linh": { to:"To: Diệu Linh", from:"Kz",
content:"Happy New Year!!!\n\nMong Linh luôn khoẻ mạnh, an yên và mọi điều đều tốt đẹp."},

"hồ nhã khánh quỳnh": { to:"To: Hồ Nhã Khánh Quỳnh", from:"Kz",
content:"Chúc Quỳnh năm mới sức khỏe, an yên và gặp nhiều điều tốt lành."},

"nguyễn ngọc bảo trâm": { to:"To: Nguyễn Ngọc Bảo Trâm", from:"Hoàng Châu",
content:"Chúc Trâm năm mới dịu dàng, bình an và mọi thứ thuận buồm xuôi gió."},

"cầm diệu châu": { to:"To: Cầm Diệu Châu", from:"Hoàng Châu",
content:"Chúc Châu năm mới vui vẻ, khoẻ mạnh và hạnh phúc mỗi ngày."},

"ksuru shirina": { to:"To: Ksuru Shirina", from:"Cu Li Chuyện Nghiệp",
content:"Chúc Shi năm mới bình an, khoẻ mạnh và thành công với con đường mình chọn."},

"lê thị linh chi": { to:"To: Lê Thị Linh Chi", from:"Kz",
content:"Chúc Chi năm mới nhiều niềm vui, sức khỏe và luôn bình an."}
};

/* ===== ALIAS ===== */
const ALIAS = {
"toastyx":"toasty x"
};

const normalize = s => s.toLowerCase().trim().replace(/\s+/g," ");

function batDau(){
 let ten = normalize(o_nhap_ten.value);
 ten = ALIAS[ten] || ten;

 if(!LOI_CHUC[ten]){
  bao_loi.style.display="block"; return;
 }

 bao_loi.style.display="none";
 man_nhap_ten.style.display="none";
 man_noi_dung.style.display="block";
 nut.style.display="block";

 const p = LOI_CHUC[ten];
 cacTrang = [
  `<span class="line-to">${p.to}</span>
   <span class="line-content">${p.content}</span>
   <span class="line-from">From: ${p.from}</span>`,
  `<span class="line-content">hey! lại gần đây đi!</span>`,
  `<span class="line-content">Kz rất tự hào về bro!</span>`
 ];
 index=0; hienThi();
}

function hienThi(){ text.innerHTML=cacTrang[index]; }
function nextTrang(){ if(index<cacTrang.length-1){index++;hienThi();}}
function prevTrang(){ if(index>0){index--;hienThi();}}
function quayVe(){
 man_nhap_ten.style.display="block";
 man_noi_dung.style.display="none";
 nut.style.display="none";
 o_nhap_ten.value="";
}
</script>
</body>
</html>
