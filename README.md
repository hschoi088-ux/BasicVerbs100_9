<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>기본동사100 스피드퀴즈</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            -webkit-user-select: none; -moz-user-select: none; -ms-user-select: none; user-select: none;
            -webkit-touch-callout: none; -webkit-tap-highlight-color: transparent;
            word-break: keep-all; background-color: #f8fafc; font-family: system-ui, -apple-system, sans-serif;
        }
        .card { perspective: 1000px; }
        .card-inner {
            position: relative; width: 100%; height: 320px;
            transition: transform 0.6s; transform-style: preserve-3d; cursor: pointer;
        }
        .card.flipped .card-inner { transform: rotateY(180deg); }
        .card-front, .card-back {
            position: absolute; width: 100%; height: 100%;
            backface-visibility: hidden; display: flex; flex-direction: column;
            align-items: center; justify-content: center; border-radius: 1.5rem;
            padding: 2rem; box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
        }
        .card-back { transform: rotateY(180deg); }
        .active-tab { background-color: #4f46e5 !important; color: white !important; }
        .star-checked { color: #fbbf24 !important; fill: #fbbf24 !important; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body oncontextmenu="return false">

    <div class="max-w-md mx-auto px-4 py-6">
        <header class="text-center mb-6">
            <h1 class="text-2xl font-black text-indigo-600 tracking-tight italic">기본동사100 스피드퀴즈</h1>
        </header>

        <div class="flex space-x-2 mb-6 overflow-x-auto pb-2 no-scrollbar font-bold text-sm">
            <button onclick="selectWeek(9)" id="tab-9" class="week-tab flex-shrink-0 px-5 py-2 bg-white rounded-full shadow-sm border border-slate-100 active-tab">Week 9</button>
            <button class="flex-shrink-0 px-5 py-2 bg-slate-100 text-slate-300 rounded-full shadow-sm border border-slate-100 cursor-not-allowed">Week 10+</button>
        </div>

        <div id="setup-screen" class="bg-white rounded-3xl p-8 shadow-xl border border-slate-100 text-center">
            <div id="selected-week-title" class="text-indigo-500 font-black text-xl mb-6 italic tracking-widest uppercase">WEEK 9</div>
            <div class="space-y-4">
                <button onclick="startQuiz('mild')" class="w-full py-5 bg-emerald-500 text-white rounded-2xl font-black shadow-lg shadow-emerald-100 active:scale-95 transition">순한맛 시작</button>
                <button onclick="startQuiz('spicy')" class="w-full py-5 bg-orange-500 text-white rounded-2xl font-black shadow-lg shadow-orange-100 active:scale-95 transition">매운맛 시작</button>
            </div>
            <div class="mt-8 p-4 bg-slate-50 rounded-xl text-[11px] text-slate-400 text-left leading-relaxed">
                <p>• <strong>순한맛</strong>: 대표예제 + Model examples</p>
                <p>• <strong>매운맛</strong>: 모든 출처 랜덤 10문항</p>
                <p class="mt-2 text-rose-400 font-bold">※ 모바일 Tip: 옆면 무음 스위치를 해제하세요!</p>
            </div>
        </div>

        <div id="quiz-screen" class="hidden">
            <div class="flex justify-between items-center mb-6 px-2">
                <span id="progress-text" class="px-3 py-1 bg-indigo-100 text-indigo-600 rounded-full text-xs font-bold">1 / 10</span>
                <button onclick="location.reload()" class="text-slate-400 font-bold text-xs uppercase">Exit ✕</button>
            </div>

            <div id="card-container" class="card" onclick="flipCard()">
                <div class="card-inner">
                    <div class="card-front bg-white border border-slate-100">
                        <span class="text-indigo-400 font-black text-[10px] tracking-widest mb-6 uppercase">Korean</span>
                        <p id="q-korean" class="text-xl font-bold leading-snug px-4 text-center"></p>
                        <p class="mt-10 text-slate-300 text-xs font-bold animate-pulse">카드를 터치하여 정답 확인</p>
                    </div>
                    <div class="card-back bg-indigo-600 text-white">
                        <button id="star-btn" onclick="toggleStar(event)" class="absolute top-6 right-6 text-white/40 transition-all">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-9 w-9" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
                                <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14l-5-4.87 6.91-1.01L12 2z"/>
                            </svg>
                        </button>
                        <span class="text-indigo-200 font-black text-[10px] tracking-widest mb-6 uppercase">English</span>
                        <p id="q-english" class="text-xl font-black leading-snug px-4 mb-8 text-center"></p>
                        <button onclick="event.stopPropagation(); playTTS();" class="w-20 h-20 bg-white/20 rounded-full flex items-center justify-center active:scale-90 transition shadow-inner">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z" />
                            </svg>
                        </button>
                        <div id="q-info" class="absolute bottom-8 text-[10px] text-indigo-300 font-bold uppercase tracking-widest italic"></div>
                    </div>
                </div>
            </div>
            <button id="next-btn" onclick="nextQuestion()" class="w-full mt-10 py-5 bg-indigo-600 text-white rounded-2xl font-black shadow-xl hidden active:scale-95 transition">다음 문제로 NEXT</button>
        </div>

        <div id="result-screen" class="hidden bg-white rounded-3xl p-6 shadow-xl border border-slate-100">
            <h2 class="text-center font-black text-xl text-slate-800 mb-8 tracking-tighter">퀴즈 완료 리포트</h2>
            <div id="starred-list" class="space-y-4 max-h-[400px] overflow-y-auto pr-2 custom-scroll"></div>
            <button onclick="location.reload()" class="w-full mt-10 py-5 bg-slate-900 text-white rounded-2xl font-bold active:scale-95 transition">메인으로 돌아가기</button>
        </div>
    </div>

    <script>
        // Week 9 전체 85개 예문 (Day 41 ~ Day 45)
        const allData = [
            // Day 41
            {w:9, d:41, s:"대표", k:"무슨 이유에서인지 화면이 어두워졌어요.", e:"The screen went dark for some reason."},
            {w:9, d:41, s:"교재1", k:"빵은 냉장 보관하면 오히려 더 빨리 굳어져 맛이 없어져요.", e:"Bread will actually go stale faster if you refrigerate it."},
            {w:9, d:41, s:"교재1", k:"오늘 아침에 버스 정류장에서 버스를 기다리다가 발이 감각이 없어졌어요.", e:"My feet went numb* waiting at the bus stop this morning."},
            {w:9, d:41, s:"교재1", k:"핸드폰 배터리가 나갔어. 충전기 있니?", e:"My phone went dead. Do you have a charger?"},
            {w:9, d:41, s:"교재1", k:"다저스가 월드 시리즈 우승을 했을 때 그는 이성을 잃었다.", e:"He went crazy when the Dodgers won the World Series."},
            {w:9, d:41, s:"교재1", k:"우리 아버지는 30대에 대머리가 되셨다.", e:"My father went bald in his thirties."},
            {w:9, d:41, s:"교재2", k:"우유가 다 떨어진 거야?", e:"Are we out of milk?"},
            {w:9, d:41, s:"교재2", k:"응, 상했더라고. 그래서 오늘 아침에 버려야만 했어.", e:"Yeah, it went bad, so I had to throw it away this morning."},
            {w:9, d:41, s:"교재2", k:"저 사람들 뭐 하고 있는 거야? 위험해 보여.", e:"What are those people doing? It looks dangerous."},
            {w:9, d:41, s:"교재2", k:"그게 바로 지난달에 화제가 된 틱톡 챌린지야.", e:"It’s that TikTok challenge that went viral last month."},
            {w:9, d:41, s:"교재2", k:"요즘은 사람들이 유명해지려고 공원까지 가는 거야? 슬프다.", e:"People go to parks to go viral these days? That’s sad."},
            {w:9, d:41, s:"교재2", k:"누가 아니래. 사람들이 그냥 맑은 공기 쐬러 공원에 갔던 때가 그리워.", e:"So true. I miss when people just went to parks to get some fresh air."},
            {w:9, d:41, s:"교재3", k:"요즘은 항상 여기저기 새롭게 쑤시고 아픈 데가 생겨. 우리 이제 늙어가네.", e:"I have new aches and pains all the time these days. We’re getting old."},
            {w:9, d:41, s:"교재3", k:"완전 공감해.", e:"So true."},
            {w:9, d:41, s:"교재3", k:"겨울에는 아침에 출근 준비하는 게 너무 힘들어.", e:"Getting ready for work in the morning is such a pain during winter."},
            {w:9, d:41, s:"교재3", k:"누가 아니래. 아침엔 정말 어둡기도 하고.", e:"That’s so true. It’s really dark, too."},
            {w:9, d:41, s:"교재3", k:"내가 어릴 때는 한국이 이렇게까지 덥지는 않았는데.", e:"Korea wasn’t this hot when I was a kid."},
            {w:9, d:41, s:"교재3", k:"내 말이!", e:"That’s so true!"},

            // Day 42
            {w:9, d:42, s:"대표", k:"음식물 쓰레기는 이 노란 봉투에 넣으면 돼요.", e:"Food trash goes in this yellow bag."},
            {w:9, d:42, s:"교재1", k:"이 꽃들은 여기에 두면 되나요?", e:"Should these flowers go here?"},
            {w:9, d:42, s:"교재1", k:"한국 국적인 분들은 이 줄에 서세요.", e:"Korean citizens go in this line."},
            {w:9, d:42, s:"교재1", k:"죄송한데, 이 경비 보고서들을 다 끝내고 나서 어디에 두면 되나요?", e:"Excuse me, where should these expense reports go when I’m done?"},
            {w:9, d:42, s:"교재1", k:"보험 서류들은 파란색 폴더에 넣으면 됩니다.", e:"Insurance papers go in the blue folder."},
            {w:9, d:42, s:"교재1", k:"포크는 접시 왼쪽에 놓습니다.", e:"The forks go to the left of the plates."},
            {w:9, d:42, s:"교재2", k:"이 덤벨 여기 두면 되나요?", e:"Do these dumbbells go here?"},
            {w:9, d:42, s:"교재2", k:"가벼운 덤벨은 맨 위에 두면 됩니다.", e:"The lighter dumbbells go on the top rack."},
            {w:9, d:42, s:"교재2", k:"내가 설거지하지 말라 그랬잖아.", e:"I told you not to do the dishes."},
            {w:9, d:42, s:"교재2", k:"내가 설거지 좋아한다고 했잖아.", e:"And I told you— I like doing the dishes."},
            {w:9, d:42, s:"교재2", k:"좋아, 그럼 내가 우리가 볼 영화를 골라 볼게.", e:"OK, well, I’ll pick a movie for us to watch."},
            {w:9, d:42, s:"교재2", k:"좋은 생각이야. 그나저나 이 와인 잔은 이 찬장에 두면 되는 거야?", e:"Good idea. By the way, do these wine glasses go in this cupboard?"},
            {w:9, d:42, s:"교재3", k:"그는 바구니에서 빨간 사과를 골랐다. (여러 개 중에 손 가는 대로, 본능적으로 골라서 집어 드는 느낌)", e:"He picked a red apple from the basket."},
            {w:9, d:42, s:"교재3", k:"네가 흥미를 느끼는 주제를 골라. (여러 개 주제 중에 끌리는 것을 뽑는 느낌)", e:"Pick a topic that interests you."},
            {w:9, d:42, s:"교재3", k:"민트 초콜릿과 팥 둘 중에 뭘 선택해야 할지 모르겠어. (두 개의 선택지를 놓고 신중하게 고민하는 느낌)", e:"I can’t choose between mint chocolate and red bean."},
            {w:9, d:42, s:"교재3", k:"결국에는 수학 공부를 선택했습니다. (고심 끝에 내린 결정의 느낌)", e:"I eventually chose to study math."},
            {w:9, d:42, s:"교재3", k:"제프가 500명의 지원자 중에서 장학금 수혜자로 뽑혔다. (격식을 갖춘 공식적인 상황에서 ‘선발된’ 느낌)", e:"Jeff was selected for the scholarship out of 500 applicants."},
            {w:9, d:42, s:"교재3", k:"이용자는 결제 전에 반드시 배송 옵션을 선택해야 합니다. (온라인 쇼핑몰 등에서 자주 등장하는 문장으로 격식을 갖춘 문어체의 느낌)", e:"The user must select a delivery option before checkout."},

            // Day 43
            {w:9, d:43, s:"대표", k:"역까지 태워 줄까요?", e:"Do you want a ride to the station?"},
            {w:9, d:43, s:"교재1", k:"저녁으로 피자 먹고 싶어요.", e:"I want pizza for dinner."},
            {w:9, d:43, s:"교재1", k:"이 케이크 한 조각 먹을래?", e:"You want a piece of this cake?"},
            {w:9, d:43, s:"교재1", k:"애들이 아이스크림 먹고 싶다네.", e:"They want ice cream."},
            {w:9, d:43, s:"교재1", k:"새 차를 사야 해서.", e:"We want a new car."},
            {w:9, d:43, s:"교재1", k:"나 저 로봇 청소기 사고 싶어.", e:"I want that robotic vacuum."},
            {w:9, d:43, s:"교재2", k:"죄송한데 창가 자리를 원해서, 예약할 때 말씀드렸는데요.", e:"Excuse me, we wanted a table by the window and mentioned that when I made a reservation."},
            {w:9, d:43, s:"교재2", k:"네, 그 점은 죄송합니다. 그런데 저희가 창가 자리는 예약을 안 받아서요.", e:"Yes, we’re sorry about that, but we don’t take reservations for those tables."},
            {w:9, d:43, s:"교재2", k:"헬스장에 있는 필라테스 수업 신청했어.", e:"I signed up for a Pilates class at the gym."},
            {w:9, d:43, s:"교재2", k:"정말? 나도 새로운 강사의 수업을 한번 들어 보고 싶었거든.", e:"Really? I have been wanting to try the new instructor’s class."},
            {w:9, d:43, s:"교재2", k:"진짜 편안한 성격에다가, 실력도 있어.", e:"She’s super chill and knows her stuff."},
            {w:9, d:43, s:"교재2", k:"그거면 됐지! 자리 예약해야겠다.", e:"That’s it, then! I’m booking a spot."},
            {w:9, d:43, s:"교재3", k:"우리 엄마가 너를 만나 보고 싶어 하셔. (몇 달 전부터 만나 보고 싶어 했으며 지금도 그렇다는 것을 강조)", e:"My mom has been wanting to meet you."},
            {w:9, d:43, s:"교재3", k:"궁 바로 옆에 있는 그 새로 생긴 카페에 가 보고 싶어. (그 카페가 생긴 후로 꼭 한번 가 보고 싶다는 마음이 이어져 왔다는 것을 강조)", e:"I’ve been wanting to try that new café next to the palace."},

            // Day 44
            {w:9, d:44, s:"대표", k:"홈페이지에는 그 제품 재고가 없다고 나와 있어요.", e:"The website says the item is out of stock."},
            {w:9, d:44, s:"교재1", k:"건강 검진 결과를 보니까 운동을 좀 더 해야 한다고 나와 있군.", e:"My health check results say I need to exercise more."},
            {w:9, d:44, s:"교재1", k:"앱에 비밀번호를 바꿔야 한다고 나와 있네.", e:"The app says I need to update my password."},
            {w:9, d:44, s:"교재1", k:"메시지 보니까 네 결제가 승인되지 않았다고 나와.", e:"The message says your payment didn’t go through."},
            {w:9, d:44, s:"교재1", k:"포장에 이탈리아 산이라고 적혀 있어.", e:"The package says it was made in Italy."},
            {w:9, d:44, s:"교재1", k:"책 뒷면에 뭐라고 적혀 있어?", e:"What does it say on the back of the book?"},
            {w:9, d:44, s:"교재2", k:"얼마나 더 가야 해?", e:"How much longer do we have?"},
            {w:9, d:44, s:"교재2", k:"내비게이션에는 한 10분 후 도착이라고 나오네.", e:"The GPS says we’ll be arriving in, like, 10 minutes."},
            {w:9, d:44, s:"교재2", k:"네가 마음에 들어 했던 핸드폰 케이스 찾았어?", e:"Did you find the phone case you liked?"},
            {w:9, d:44, s:"교재2", k:"응, 근데 홈페이지에는 품절로 나와.", e:"Yeah, but the website says it’s out of stock."},
            {w:9, d:44, s:"교재2", k:"하아, 또?", e:"Ugh, again?"},
            {w:9, d:44, s:"교재2", k:"아래쪽에 보니, 다음 주에 재입고 예정이라고 나와 있네.", e:"Further down, it says they should have it back in stock next week."},
            {w:9, d:44, s:"교재3", k:"이번 주말쯤이면 날씨가 갤 것 같아.", e:"The weather should clear up by this weekend."},
            {w:9, d:44, s:"교재3", k:"(주문한) 음식이 곧 배달될 것 같은데.", e:"The food should be here any minute."},
            {w:9, d:44, s:"교재3", k:"그 시간쯤에는 차가 그렇게 많지 않을 거예요.", e:"There shouldn’t be any traffic around that time."},
            {w:9, d:44, s:"교재3", k:"집까지 가는 데 그렇게 오래 안 걸릴 것 같아요.", e:"It shouldn’t take me long to get home."},
            {w:9, d:44, s:"교재3", k:"회의를 내일 오후로 조정할 수 있을까요?", e:"Do you think I can reschedule the meeting for tomorrow afternoon?"},
            {w:9, d:44, s:"교재3", k:"물론이죠, 큰 문제 없을 것 같습니다.", e:"Sure, that shouldn’t be a problem."},
            {w:9, d:44, s:"교재3", k:"소프트웨어 업데이트하는 데 얼마나 걸릴까요?", e:"How long will it take to update the software?"},
            {w:9, d:44, s:"교재3", k:"그렇게 오래 안 걸릴 거예요. 몇 분이면 됩니다.", e:"It shouldn’t take long; just a few minutes or so."},

            // Day 45
            {w:9, d:45, s:"대표", k:"제가 문까지 바래다드릴게요.", e:"Let me walk you out."},
            {w:9, d:45, s:"교재1", k:"제가 한잔 살게요.", e:"Let me buy you a drink."},
            {w:9, d:45, s:"교재1", k:"내가 같이 가 줄게.", e:"Let me go with you."},
            {w:9, d:45, s:"교재1", k:"한번 볼게요.", e:"Let me take a look."},
            {w:9, d:45, s:"교재1", k:"내가 대신 그 박스 옮겨 줄게.", e:"Let me carry that box for you."},
            {w:9, d:45, s:"교재1", k:"제가 와인 한 잔 더 따라 드릴게요.", e:"Let me pour you another glass of wine."},
            {w:9, d:45, s:"교재2", k:"나 혼자서 이 모든 걸 차로 옮길 수 있을 것 같아.", e:"I think I can carry it all to the car by myself."},
            {w:9, d:45, s:"교재2", k:"알았어, 그럼 내가 문 열어서 잡고 있을게.", e:"OK, well, let me get the door for you."},
            {w:9, d:45, s:"교재2", k:"(강원도 여행 후 서울로 가려는 상황) 다시 서울로 가려면 5시간은 걸릴 듯해.", e:"It’s going to take five hours to get back to the city."},
            {w:9, d:45, s:"교재2", k:"돌아가는 길이 항상 더 막히지.", e:"Traffic is always worse going back."},
            {w:9, d:45, s:"교재2", k:"맞아. 지금 출발하는 게 낫겠다.", e:"Yeah, true. We better get going."},
            {w:9, d:45, s:"교재2", k:"운전은 내가 할게. 당신은 쉬어.", e:"Let me drive. You can rest."},
            {w:9, d:45, s:"교재3", k:"집으로 가 봐야 해.", e:"I need to head home."},
            {w:9, d:45, s:"교재3", k:"집으로 가 봐야 할 시간이야.", e:"It’s time to head home."},
            {w:9, d:45, s:"교재3", k:"늦었다. 오늘은 이쯤에서 마무리하자.", e:"It’s getting late. Let’s call it a night."},
            {w:9, d:45, s:"교재3", k:"아내에게서 전화가 와. 이제 가 봐야겠다.", e:"My wife is calling. I better get going."},
            {w:9, d:45, s:"교재3", k:"그래, 오늘은 여기까지 하자고.", e:"Yeah, let’s call it a night."}
        ];

        // 고유 ID 부여
        allData.forEach((item, index) => item.id = index + 1);

        const sourceMap = { "대표": "대표예제", "교재1": "Model examples", "교재2": "Small talk", "교재3": "Further studies" };
        let selectedWeek = 9; // 9주차로 세팅
        let quizPool = [];
        let currentIndex = 0;
        let starredIds = new Set();

        function selectWeek(w) {
            selectedWeek = w;
            document.querySelectorAll('.week-tab').forEach(t => t.classList.remove('active-tab'));
            document.getElementById(`tab-${w}`).classList.add('active-tab');
        }

        // 모바일 오디오 깨우기: 더 강력한 방식
        function unlockAudio() {
            if ('speechSynthesis' in window) {
                const msg = new SpeechSynthesisUtterance("Quiz Start");
                msg.volume = 0;
                msg.rate = 10;
                window.speechSynthesis.speak(msg);
                window.speechSynthesis.cancel(); // 즉시 취소하여 엔진만 활성화
            }
        }

        function startQuiz(mode) {
            unlockAudio(); // 버튼 클릭 시 엔진 잠금 해제

            let weekData = allData.filter(item => item.w === selectedWeek);
            let filtered = (mode === 'mild') 
                ? weekData.filter(item => item.s === '대표' || item.s === '교재1') 
                : [...weekData];

            // 랜덤 10개 추출
            quizPool = filtered.sort(() => Math.random() - 0.5).slice(0, 10);
            currentIndex = 0;
            starredIds.clear();
            
            document.getElementById('setup-screen').classList.add('hidden');
            document.getElementById('quiz-screen').classList.remove('hidden');
            showQuestion();
        }

        function showQuestion() {
            const item = quizPool[currentIndex];
            const card = document.getElementById('card-container');
            const star = document.getElementById('star-btn');
            
            card.classList.remove('flipped');
            star.classList.remove('star-checked');
            document.getElementById('next-btn').classList.add('hidden');
            
            document.getElementById('q-korean').innerText = item.k;
            document.getElementById('q-english').innerText = item.e;
            document.getElementById('q-info').innerText = `Day ${String(item.d).padStart(3, '0')} - ${sourceMap[item.s]}`;
            document.getElementById('progress-text').innerText = `${currentIndex + 1} / ${quizPool.length}`;
        }

        function flipCard() {
            document.getElementById('card-container').classList.add('flipped');
            document.getElementById('next-btn').classList.remove('hidden');
        }

        function toggleStar(e) {
            e.stopPropagation();
            const item = quizPool[currentIndex];
            const starBtn = document.getElementById('star-btn');
            if (starredIds.has(item.id)) {
                starredIds.delete(item.id);
                starBtn.classList.remove('star-checked');
            } else {
                starredIds.add(item.id);
                starBtn.classList.add('star-checked');
            }
        }

        function playTTS() {
            const text = document.getElementById('q-english').innerText;
            if ('speechSynthesis' in window) {
                // 이전 대기 음성 모두 제거
                window.speechSynthesis.cancel();

                // iOS에서 새 인스턴스 생성이 필요할 수 있음
                const msg = new SpeechSynthesisUtterance();
                msg.text = text;
                msg.lang = 'en-US';
                msg.rate = 0.9;
                
                // 음성 목록 로딩 (iOS Safari 대응)
                let voices = window.speechSynthesis.getVoices();
                if (voices.length > 0) {
                    msg.voice = voices.find(v => v.lang.includes('en-US')) || voices[0];
                }

                window.speechSynthesis.speak(msg);
            }
        }

        function nextQuestion() {
            currentIndex++;
            if (currentIndex < quizPool.length) showQuestion();
            else showResults();
        }

        function showResults() {
            document.getElementById('quiz-screen').classList.add('hidden');
            document.getElementById('result-screen').classList.remove('hidden');
            const listEl = document.getElementById('starred-list');
            listEl.innerHTML = '<p class="text-[10px] text-slate-400 font-black mb-4 uppercase tracking-widest text-center">⭐ 다시 학습할 문장</p>';
            
            const starredItems = quizPool.filter(item => starredIds.has(item.id));
            if (starredItems.length === 0) {
                listEl.innerHTML += '<p class="text-sm text-slate-400 py-12 text-center font-bold">체크한 문장이 없네요!</p>';
            } else {
                starredItems.forEach(item => {
                    const div = document.createElement('div');
                    div.className = "p-5 bg-indigo-50 rounded-2xl border border-indigo-100 mb-3";
                    div.innerHTML = `
                        <div class="flex justify-between items-start mb-2">
                            <span class="text-[9px] bg-indigo-500 text-white px-1.5 py-0.5 rounded font-black tracking-tighter">DAY ${item.d}</span>
                            <span class="text-[9px] text-indigo-400 font-bold italic">${sourceMap[item.s]}</span>
                        </div>
                        <p class="text-xs text-slate-500 mb-1 leading-relaxed">${item.k}</p>
                        <p class="text-sm font-black text-indigo-900 leading-snug">${item.e}</p>
                    `;
                    listEl.appendChild(div);
                });
            }
        }

        // 보안: 우클릭/복사 방지
        document.addEventListener('contextmenu', e => e.preventDefault());
        document.addEventListener('keydown', e => {
            if (e.ctrlKey && (e.key === 'c' || e.key === 'u' || e.key === 's')) e.preventDefault();
            if (e.key === 'F12') e.preventDefault();
        });
    </script>
</body>
</html>
