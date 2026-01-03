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
    flex-direction: column;
    justify-content: center;
    align-items: center;
    font-family: 'Patrick Hand', cursive;
    background-color: var(--pixel-bg);
    background-image: radial-gradient(var(--pixel-red) 2px, transparent 2px);
    background-size: 20px 20px;
    color: var(--pixel-yellow);
    overflow: hidden;
}

.pixel-box {
    background: white;
    border: 4px solid var(--pixel-dark);
    box-shadow: 4px 4px 0 0 var(--pixel-dark), 8px 8px 0 0 rgba(0,0,0,0.3);
    padding: 25px;
    color: var(--pixel-dark);
    max-width: 500px;
    width: 85%;
    position: relative;
    max-height: 75vh;
    overflow-y: auto;
}

#man-nhap-ten { text-align: center; }
#man-nhap-ten p { font-size: 2.2rem; text-shadow: 2px 2px var(--pixel-dark); margin-bottom: 20px; }

input {
    font-family: 'Patrick Hand', cursive;
    padding: 12px;
    font-size: 1.4rem;
    border: 3px solid var(--pixel-dark);
    outline: none;
    text-align: center;
    width: 250px;
    max-width: 90%;
}

.pixel-btn {
    font-family: 'Patrick Hand', cursive;
    background: var(--pixel-yellow);
    color: var(--pixel-dark);
    border: 3px solid var(--pixel-dark);
    box-shadow: 4px 4px 0 0 var(--pixel-dark);
    padding: 8px 20px;
    font-size: 1.4rem;
    cursor: pointer;
    font-weight: bold;
    margin-top: 20px;
}

.pixel-btn:active { transform: translate(2px, 2px); box-shadow: 0 0 0 0; }

.line-to { font-weight: bold; font-size: 1.8rem; color: var(--pixel-red); margin-bottom: 10px; display: block; }
.line-content { 
    text-align: center; 
    font-size: 1.4rem; 
    line-height: 1.5; 
    margin: 15px 0; 
    display: block; 
    white-space: pre-line !important;
}
.line-from { font-style: italic; font-size: 1.2rem; color: #555; display: block; margin-top: 10px; }

#man-noi-dung { display: none; }
#nut { display: none; text-align: center; margin-top: 20px; }
#nut button { margin: 5px; }

#bao-loi { color: #fff; background: var(--pixel-red); margin-top: 15px; padding: 10px; font-size: 1.2rem; display: none; border: 2px solid var(--pixel-dark); }

@media (max-width: 600px) {
    #man-nhap-ten p { font-size: 1.8rem; }
    .line-to { font-size: 1.5rem; }
    .line-content { font-size: 1.2rem; }
    .pixel-box { padding: 15px; width: 88%; }
}
</style>
</head>
<body>

<div id="man-nhap-ten">
    <p>NHẬP TÊN CỦA BẠN</p>
    <input id="o-nhap-ten" type="text" placeholder="Ví dụ: Nguyễn Văn Mười">
    <br>
    <button class="pixel-btn" onclick="batDau()">XÁC NHẬN →</button>
    <div id="bao-loi">TÊN KHÔNG CÓ TRONG DANH SÁCH!</div>
</div>

<div id="man-noi-dung" class="pixel-box">
    <div id="text"></div>
</div>

<div id="nut">
    <button id="nut-prev" class="pixel-btn" onclick="prevTrang()">⟵</button>
    <button id="nut-next" class="pixel-btn" onclick="nextTrang()">⟶</button>
    <button class="pixel-btn" style="background: white;" onclick="quayVe()">⟲</button>
</div>

<script>
let cacTrang = [];
let index = 0;

const LOI_CHUC = {
    "toasty x": { to: "To: Toasty X", from: "Kz",content: "Chúc Tọt Te năm mới nhiều sức khỏe. Bình an và đạt được những điều mình mong mới trong năm nay! Chúc cho Tọt Te thật nhiều sức khỏe để có thể tiếp tục còn đường mình đã chọn! Mong mọi thứ sẽ nhẹ nhàng với Tọt Te hơn trong năm nay và mong mọi điều tốt đẹp sẽ đến với Tọt Te trong thời gian tới!" 
    },
    "phạm hải anh": { to: "To: Phạm Hải Anh", from: "Ka dé (⁠ ˘⁠ ³⁠˘⁠)", content: "Akemashite omedetooooo\n\nChúc Hải Anh năm mới sẽ có thêm nhiều ngày vui cho riêng mình, luôn giữ được sức khoẻ. Công việc thuận lợi, cuộc sống ổn định và mọi thứ đều tốt đẹp." },
    "đỗ phương anh": { to: "To: Đỗ Phương Anh", from: " Kz", content: "Happy New Year\n\nNăm mới rồi, mong Phương Anh sẽ dịu dàng hơn với chính mình. Chúc Phương Anh luôn có sức khoẻ, có người để tin tưởng và có điều để hy vọng. cũng mong Phương Anh sẽ hứa những điều này với mình chứ Không phải với tui. Vẫn như cũ, vạn năm nghìn ngày mong lòng người luôn ngập nắng." },
    "nguyễn kim ngân": { to: "To: Nguyễn Kim Ngân", from: " Ka Văn Dé (⁠ ◜⁠‿⁠◝⁠ )⁠♡", content: "Happy New Year\n\nChúc Ngân năm mới nhiều sức khỏe, nhiều niềm vui và thật nhiều điều tốt lành. Mong là mọi mệt mỏi của năm cũ sẽ ở lại phía sau, và hy vọng năm mới Ngân có thể cười nhiều hơn nữa. Mong là dù là nắng hay mưa thì mỗi ngày vẫn sẽ là ngày vui đối với Ngân." },
    "hoàng ngọc dương": { to: "To: Hoàng Ngọc Dương", from: "Gà Ủ Muối.", content: "ChÚc MừNg NăM mỚi\n\nNăm mới chúc anh Dương luôn an yên, khoẻ mạnh, ăn no, ngủ kỹ, làm ăn phát tài, vạn sự đại cát, và vẫn còn dư dả thời gian để em còn làm phiền anh đều đều ạ=)))\n\nMong là năm mới anh có dịp để ra Sài gòn chơi với tụi em╰⁠(⁠＾⁠3⁠＾⁠)⁠╯!" },
    "nguyễn đức minh quang": { to: "To: Nguyễn Đức Minh Quang", from: " \"con bé Kz\"", content: "Happy New Yearrr\n\nChúc anh Quang năm mới an khang, Khoẻ mạnh, tinh thần luôn thoải mái và lúc nào cũng giữ được năng lượng tích cực. Mong là năm nay Quang sẽ có nhiều cơ hội trong công việc hơn nữa để lì xì tụi em :))\n\nHứa năm mới sẽ gọi là \"anh Quang\" nhiều hơn năm cũ để không bị doạ đánh nữa =)))" },
    "nguyễn anh nhật": { to: "To: Nguyễn Anh Nhật", from: "Quỷ Nhỏ", content: "Chúc Mừng Năm Mới!\n\nChúc anh Nhật năm mới thật nhiều sức khỏe và bình an. Mong là những điều anh mong chờ sẽ dần thực hiện được trong năm mới. Và mọi đều tốt đẹp sẽ đến với anh trong năm nay. Cũng cảm ơn anh rất nhiều vì đã giúp đỡ và để em làm phiền trong suốt thời gian qua⊂⁠(⁠(⁠・⁠▽⁠・⁠)⁠)⁠⊃!!" },
    "phan tiến thịnh": { to: "To: Phan Tiến Thịnh", from: "Hoàng Vũ Kiên Giang Vịnh Thái Lán Phú Quốc", content: "Happy New Year!\n\nChúc chú Hữu năm mới thật nhiều niềm vui và sức khoẻ. Cảm ơn chú vì đã luôn dễ thương và tử tế với cháu trong suốt thời gian qua : 3 Mong năm mới vẫn sẽ được đồng hành, học hỏi thêm nhiều điều từ chú!!, cũng chúc cho chị Vàng và chị Mực có nhiều sức khoẻ ạ." },
    "diệu linh": { to: "To: Diệu Linh", from: "Kz", content: "Happy New Year!!!\n\nNăm mới mong Diệu Linh luôn giữ được sức Khoẻ và có những ngày an yên hơn. Chúc Diệu Linh mọi đều tốt đẹp trong thời gian tới." },
    "hồ nhã khánh quỳnh": { to: "To: Hồ Nhã Khánh Quỳnh", from: "Kz", content: "Chúc Quỳnh năm mới nhiều sức khỏe, an yên, học giỏi. Mong là năm mới Quỳnh không gặp phải những mối quan hệ khiến Quỳnh phải suy nghĩ nữa. Mong là Quỳnh sẽ càng được thêm nhiều người quý mến trong năm nay. Mong mọi điều tốt đẹp sẽ đến với Quỳnh trong năm nay. (⁠ ◜⁠‿⁠◝⁠ )⁠♡" },
    "nguyễn ngọc bảo trâm": { to: "To: Nguyễn Ngọc Bảo Trâm", from: "Hoàng Châu", content: "Chúc Trâm năm mới nhiều sức khỏe. Nhiều niềm vui trong cuộc sống. Mong mọi thứ sẽ thuận buồm xuôi gió, dịu dàng, ấm áp với Trâm. Chúc Trâm có một năm trọn vẹn, bình an. (⁠っ⁠˘⁠з⁠(⁠˘⁠⌣⁠˘⁠ )" },
    "cầm diệu châu": { to: "To: Cầm Diệu Châu", from: "Hoàng Châu.", content: "Chúc Châu năm mới vui vẻ. Mong năm nay sẽ không có nhiều thứ tiêu cực đến với Châu. Mong Châu có sức khỏe, có bình an và có điều để tin tưởng, có người để dựa vào. Mong Châu hạnh phúc, thành công và vui vẻ. Chúc Châu ngày nào trong năm lòng cũng an yên, cũng tươi đẹp:-P" },
    "ksuru shirina": { to: "To: Ksuru Shirina", from: "Cu Li Chuyện Nghiệp", content: "Chúc Shi năm mới bình an, khoẻ mạnh, Vạn Sự Đại Cát. Mong những ngày của năm nay sẽ nhẹ nhàng với Shi. Chúc cho Shi thành công với những con đường mình đã chọn và luôn có điều để Shi có thể tin tưởng cũng như dựa vào.(⁠ ◜⁠‿⁠◝⁠ )⁠♡" },
    "lê thị linh chi": { to: "To: Lê Thị Linh Chi", from: "Kz", content: "Chúc Chi năm mới nhiều niềm vui. Nhiều sức khỏe và mong những ngày mới của năm sẽ nhẹ nhàng với Chi hơn một chút. Chúc Chi luôn bình an, xinh đẹp và hạnh phúc(⁠ ◜⁠‿⁠◝⁠ )⁠♡" }
};

function batDau() {
    const ten = document.getElementById("o-nhap-ten").value.trim().toLowerCase();
    if (!LOI_CHUC[ten]) {
        document.getElementById("bao-loi").style.display = "block";
        return;
    }
    document.getElementById("man-nhap-ten").style.display = "none";
    document.getElementById("man-noi-dung").style.display = "block";
    document.getElementById("nut").style.display = "block";

    const p = LOI_CHUC[ten];
    cacTrang = [
        `<span class="line-to">${p.to}</span><span class="line-content">${p.content}</span><span class="line-from">From: ${p.from || 'Kz'}</span>`,
        `<span class="line-content">hey! lại gần đây đi!</span>`,
        `<span class="line-content">khịt khịt.... Bro có mùi của....</span>`,
        `<span class="line-content">Một người đã cố gắng hết mình!\nKz rất tự hào về bro!</span>`,
        `<span class="line-content">Thành tựu lớn nhất năm nay của Kz là gặp được những người bạn mới và những người bạn cũ vẫn còn ở đây với kz.\nnăm mới mong mọi người đều khoẻ mạnh để có sức quậy cùng kz.\nKz quý mọi người lắm ạ(⁠ ◜⁠‿⁠◝⁠ )⁠♡</span>`
    ];
    index = 0;
    hienThi();
}

function hienThi() {
    document.getElementById("text").innerHTML = cacTrang[index];
    document.getElementById("nut-prev").style.visibility = index > 0 ? "visible" : "hidden";
    document.getElementById("nut-next").style.visibility = index < cacTrang.length - 1 ? "visible" : "hidden";
}

function nextTrang() { if (index < cacTrang.length - 1) { index++; hienThi(); } }
function prevTrang() { if (index > 0) { index--; hienThi(); } }
function quayVe() {
    document.getElementById("man-nhap-ten").style.display = "block";
    document.getElementById("man-noi-dung").style.display = "none";
    document.getElementById("nut").style.display = "none";
    document.getElementById("o-nhap-ten").value = "";
    document.getElementById("bao-loi").style.display = "none";
}
</script>
</body>
</html>
