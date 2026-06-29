const CONFIG = {
    namaPasangan: "Sayangku",
    tanggalJadian: "2024-10-15", // Format: YYYY-MM-DD
    musikPlaylist: [
        "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" // Ganti URL MP3 lagu romantis di sini
    ],
    ceritaVisualNovel: [
        "Hai Sayang... ❤️",
        "Aku buat halaman ini khusus untuk nunjukin seberapa sayang aku ke kamu.",
        "Tapi sebelum masuk ke menu utama, ada beberapa tantangan kecil yang harus kamu lewatin dulu ya! Hehe 🤭"
    ],
    suratCinta: "Halo manisku, terima kasih ya sudah bertahan dan berjalan bersamaku sejauh ini. Setiap hari bersamamu adalah berkah luar biasa bagiku. Tetap jadi orang yang sama, yang selalu membuatku tersenyum. I Love You Forever! 💖",
    galeriFoto: [
        "https://picsum.photos/id/1025/300/200", // Ganti dengan URL fotomu
        "https://picsum.photos/id/103/300/200"
    ],
    timelineKenangan: [
        { tanggal: "15 Okt 2024", judul: "Hari Jadian Kita", desc: "Hari paling bahagia saat kamu nerima aku." },
        { tanggal: "25 Des 2024", judul: "Jalan Pertama", desc: "Momen gugup tapi seru banget bareng kamu." }
    ],
    puzzleImage: "https://picsum.photos/id/1062/300/300" // Gambar untuk mini game puzzle
};

// ==========================================
// CORE SYSTEM VARIABLE
// ==========================================
let currentStoryIndex = 0;
let currentGameIndex = 0;
let score = 0;
let gameTimer = null;

// DOM Elements
const loadScreen = document.getElementById('loading-screen');
const storyScreen = document.getElementById('story-screen');
const gameScreen = document.getElementById('game-screen');
const finalScreen = document.getElementById('final-screen');
const bgMusic = document.getElementById('bg-music');

// ==========================================
// INISIALISASI & KONTROL MUSIK / BACKROUND
// ==========================================
window.addEventListener('DOMContentLoaded', () => {
    bgMusic.src = CONFIG.musikPlaylist[0];
    initBackgroundEffects();
    initCursor();
});

document.getElementById('start-btn').addEventListener('click', () => {
    bgMusic.play().catch(() => console.log("Audio play diblokir browser, butuh interaksi."));
    document.getElementById('music-toggle').innerText = "🎵 Pause";
    loadScreen.classList.remove('active');
    storyScreen.classList.add('active');
    showStory();
});

document.getElementById('music-toggle').addEventListener('click', () => {
    if(bgMusic.paused) {
        bgMusic.play();
        document.getElementById('music-toggle').innerText = "🎵 Pause";
    } else {
        bgMusic.pause();
        document.getElementById('music-toggle').innerText = "🎵 Play";
    }
});

// Efek Cursor & Kelopak Bunga Berjatuhan
function initCursor() {
    window.addEventListener('mousemove', (e) => {
        const cursor = document.getElementById('heart-cursor');
        cursor.style.left = e.clientX + 'px';
        cursor.style.top = e.clientY + 'px';
    });
}

function initBackgroundEffects() {
    const bg = document.getElementById('bg-animation');
    const items = ['🌸', '❤️', '✨', '💖'];
    setInterval(() => {
        const el = document.createElement('div');
        el.className = 'floating-element';
        el.innerText = items[Math.floor(Math.random() * items.length)];
        el.style.left = Math.random() * 100 + 'vw';
        el.style.animationDuration = (Math.random() * 3 + 4) + 's';
        bg.appendChild(el);
        setTimeout(() => el.remove(), 7000);
    }, 400);
}

// ==========================================
// VISUAL NOVEL SYSTEM
// ==========================================
function showStory() {
    if(currentStoryIndex < CONFIG.ceritaVisualNovel.length) {
        document.getElementById('story-text').innerText = CONFIG.ceritaVisualNovel[currentStoryIndex];
    } else {
        storyScreen.classList.remove('active');
        gameScreen.classList.add('active');
        loadGameLevel();
    }
}

document.getElementById('next-story-btn').addEventListener('click', () => {
    currentStoryIndex++;
    showStory();
});

// ==========================================
// MINI GAME MANAGER
// ==========================================
function loadGameLevel() {
    const container = document.getElementById('game-container');
    const title = document.getElementById('game-title');
    const desc = document.getElementById('game-desc');
    const nextBtn = document.getElementById('next-game-btn');
    
    container.innerHTML = "";
    nextBtn.classList.add('hidden');
    clearInterval(gameTimer);

    switch(currentGameIndex) {
        case 0:
            title.innerText = "Game 1: Cari Hati Tersembunyi";
            desc.innerText = "Temukan 5 hati merah muda di dalam kotak ini!";
            score = 0;
            for(let i=0; i<5; i++) {
                let heart = document.createElement('span');
                heart.className = 'hidden-heart';
                heart.innerText = '💗';
                heart.style.top = Math.random() * 80 + 10 + '%';
                heart.style.left = Math.random() * 80 + 10 + '%';
                heart.onclick = () => {
                    heart.style.visibility = 'hidden';
                    score++;
                    if(score === 5) { desc.innerText = "Hebat! Kamu nemu semuanya 🥰"; nextBtn.classList.remove('hidden'); }
                }
                container.appendChild(heart);
            }
            break;

        case 1:
            title.innerText = "Game 2: Pertanyaan Romantis";
            desc.innerText = "Jawab dengan jujur ya!";
            container.innerHTML = `
                <div style="display:flex; flex-direction:column; gap:10px; width:100%">
                    <p>Mau terus bersamaku selamanya?</p>
                    <button class="btn" id="ans-yes">Mau Banget! 💕</button>
                    <button class="btn" id="ans-no" style="position:relative;">Nggak Mau 😜</button>
                </div>
            `;
            const noBtn = document.getElementById('ans-no');
            noBtn.onmouseover = () => {
                noBtn.style.top = Math.random() * 100 - 50 + 'px';
                noBtn.style.left = Math.random() * 100 - 50 + 'px';
            };
            document.getElementById('ans-yes').onclick = () => {
                desc.innerText = "Aku juga mau terus sama kamu! Akhhh sayang banget! 😍";
                nextBtn.classList.remove('hidden');
            }
            break;

        case 2:
            title.innerText = "Game 3: Tangkap Hati Berjatuhan";
            desc.innerText = "Tangkap 5 hati jatuh dalam waktu berjalan!";
            score = 0;
            gameTimer = setInterval(() => {
                let h = document.createElement('span');
                h.className = 'catch-heart';
                h.innerText = '❤️';
                h.style.left = Math.random() * 90 + '%';
                h.onclick = () => {
                    h.remove();
                    score++;
                    if(score >= 5) {
                        desc.innerText = "Target tercapai! Kamu jago menangkap hatiku 😜";
                        clearInterval(gameTimer);
                        nextBtn.classList.remove('hidden');
                    }
                }
                container.appendChild(h);
                setTimeout(() => h.remove(), 2000);
            }, 800);
            break;

        default:
            gameScreen.classList.remove('active');
            finalScreen.style.display = 'block';
            initFinalScreen();
            break;
    }
}

document.getElementById('next-game-btn').addEventListener('click', () => {
    currentGameIndex++;
    loadGameLevel();
});

// ==========================================
// FINAL SCREEN SHOWCASE
// ==========================================
function initFinalScreen() {
    // Efek Mengetik Surat Cinta
    const letterEl = document.getElementById('love-letter');
    let textIdx = 0;
    letterEl.innerText = "";
    function type() {
        if(textIdx < CONFIG.suratCinta.length) {
            letterEl.innerHTML += CONFIG.suratCinta.charAt(textIdx);
            textIdx++;
            setTimeout(type, 50);
        }
    }
    type();

    // Setup Countdown
    setInterval(() => {
        const diff = new Date() - new Date(CONFIG.tanggalJadian);
        const hari = Math.floor(diff / (1000 * 60 * 60 * 24));
        document.getElementById('countdown').innerText = `${hari} Hari Bersama ❤️`;
    }, 1000);

    // Setup Galeri
    const gal = document.getElementById('photo-gallery');
    gal.innerHTML = "";
    CONFIG.galeriFoto.forEach(url => {
        let img = document.createElement('img');
        img.src = url;
        gal.appendChild(img);
    });

    // Setup Timeline
    const tl = document.getElementById('love-timeline');
    tl.innerHTML = "";
    CONFIG.timelineKenangan.forEach(item => {
        tl.innerHTML += `
            <div class="timeline-item">
                <h4>${item.tanggal} - ${item.judul}</h4>
                <p>${item.desc}</p>
            </div>
        `;
    });
}

// INTERACTIVE ACTION BUTTONS
function triggerVisualEffect(emoji) {
    const overlay = document.getElementById('interaction-overlay');
    const h = document.createElement('div');
    h.className = 'big-heart-anim';
    h.innerText = emoji;
    overlay.appendChild(h);
    setTimeout(() => h.remove(), 1000);
}

document.getElementById('btn-peluk').onclick = () => triggerVisualEffect('🤗');
document.getElementById('btn-cium').onclick = () => triggerVisualEffect('💋');
document.getElementById('btn-sayang').onclick = () => triggerVisualEffect('💖');
document.getElementById('btn-restart').onclick = () => location.reload();
