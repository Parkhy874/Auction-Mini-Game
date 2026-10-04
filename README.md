# Auction-Mini-Game
Based by 'Neverless To Everless

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>이환 - 땅땅땅 블라인드 경매</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        cyberDark: '#07090F',
                        cyberCard: '#111726',
                        cyberBorder: '#222E46',
                        neonCyan: '#00F0FF',
                        neonPink: '#FF0055',
                        neonGold: '#FFB800',
                        neonPurple: '#A855F7'
                    },
                    fontFamily: {
                        sans: ['Inter', 'Noto Sans KR', 'sans-serif']
                    }
                }
            }
        }
    </script>
    <!-- Google Fonts & Font Awesome Icons -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&family=Noto+Sans+KR:wght@400;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        body {
            background-color: #05070C;
            color: #E2E8F0;
            font-family: 'Noto Sans KR', 'Inter', sans-serif;
            overflow-x: hidden;
        }

        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #07090F;
        }
        ::-webkit-scrollbar-thumb {
            background: #222E46;
            border-radius: 4px;
        }

        .glow-cyan { box-shadow: 0 0 20px rgba(0, 240, 255, 0.3); }
        .glow-gold { box-shadow: 0 0 20px rgba(255, 184, 0, 0.3); }
        .text-glow-cyan { text-shadow: 0 0 10px rgba(0, 240, 255, 0.7); }
        .text-glow-gold { text-shadow: 0 0 10px rgba(255, 184, 0, 0.7); }

        @keyframes hammerStrike {
            0% { transform: rotate(-50deg) scale(0.4); opacity: 0; }
            50% { transform: rotate(15deg) scale(1.2); opacity: 1; }
            70% { transform: rotate(-5deg) scale(1.0); }
            100% { transform: rotate(0deg) scale(1); opacity: 1; }
        }

        .animate-hammer {
            animation: hammerStrike 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        .scanline {
            background: linear-gradient(
                to bottom,
                rgba(255,255,255,0),
                rgba(255,255,255,0) 50%,
                rgba(0, 240, 255, 0.03) 50%,
                rgba(0, 240, 255, 0.03)
            );
            background-size: 100% 4px;
        }

        .door-left-open {
            transform: translateX(-100%);
            transition: transform 1.2s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .door-right-open {
            transform: translateX(100%);
            transition: transform 1.2s cubic-bezier(0.4, 0, 0.2, 1);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between selection:bg-neonCyan selection:text-black">

    <header class="border-b border-cyberBorder bg-cyberDark/90 backdrop-blur-md sticky top-0 z-40 px-6 py-4">
        <div class="max-w-7xl mx-auto flex flex-wrap justify-between items-center gap-4">
            <div class="flex items-center space-x-4">
                <div class="w-11 h-11 rounded-xl bg-gradient-to-tr from-neonCyan to-neonPurple flex items-center justify-center font-black text-black text-2xl shadow-lg">
                    땅
                </div>
                <div>
                    <h1 class="text-2xl font-black tracking-wider text-white flex items-center gap-2">
                        이환 <span class="text-xs px-2.5 py-0.5 rounded bg-neonCyan/20 text-neonCyan border border-neonCyan/40">땅땅땅 블라인드 경매</span>
                    </h1>
                    <p class="text-xs text-gray-400">비밀 입찰 방식 | 최대 1,000,000원 상한 | 2.7배 조기 낙찰 시스템</p>
                </div>
            </div>

            <div class="flex items-center gap-6 bg-cyberCard border border-cyberBorder px-5 py-2.5 rounded-xl shadow-md">
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-wallet text-neonGold text-base"></i>
                    <span class="text-xs text-gray-400">내 소지금:</span>
                    <span id="playerMoneyDisplay" class="font-black text-neonGold text-lg">₩1,000,000</span>
                </div>
                <div class="h-5 w-px bg-cyberBorder"></div>
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-chart-line text-neonCyan text-base"></i>
                    <span class="text-xs text-gray-400">누적 손익:</span>
                    <span id="totalProfitDisplay" class="font-black text-neonCyan text-lg">₩0</span>
                </div>
            </div>
        </div>
    </header>

    <main class="max-w-7xl w-full mx-auto p-4 md:p-6 grid grid-cols-1 lg:grid-cols-12 gap-6 flex-1">

        <!-- LEFT PANEL: Compact Intel Board -->
        <aside class="lg:col-span-3 bg-cyberCard border border-cyberBorder rounded-2xl p-4 flex flex-col justify-between shadow-xl relative overflow-hidden h-fit max-h-[380px]">
            <div class="scanline absolute inset-0 pointer-events-none"></div>
            
            <div>
                <div class="flex items-center justify-between pb-2.5 mb-3 border-b border-cyberBorder">
                    <h2 class="text-xs font-bold text-neonCyan flex items-center gap-1.5">
                        <i class="fa-solid fa-clipboard-check"></i> 정보 조사 노트
                    </h2>
                    <span class="text-[10px] bg-cyberDark px-2 py-0.5 rounded text-gray-400 border border-cyberBorder">
                        <i class="fa-solid fa-user-secret text-neonGold mr-1"></i>정보원
                    </span>
                </div>

                <div class="space-y-2 mb-3">
                    <div class="text-[11px] font-semibold text-gray-400 uppercase tracking-wider">창고 첩보 소식</div>
                    <ul id="rumorList" class="space-y-1.5 text-[11px] max-h-36 overflow-y-auto pr-1">
                        <!-- Dynamic Rumors -->
                    </ul>
                </div>

                <div class="bg-cyberDark border border-cyberBorder p-3 rounded-xl mb-3">
                    <div class="flex justify-between items-center text-[10px] mb-0.5">
                        <span class="text-gray-400">추정 가치 범위 (최대 100만)</span>
                        <span id="confidenceTag" class="text-neonGold font-bold text-[10px]">신뢰도 A</span>
                    </div>
                    <div id="estimatedRangeText" class="text-lg font-black text-white text-glow-cyan">
                        ₩200,000 ~ ₩600,000
                    </div>
                </div>
            </div>

            <div class="pt-2.5 border-t border-cyberBorder text-[11px] text-gray-400 space-y-1">
                <div class="flex justify-between items-center">
                    <span>매치 진행:</span>
                    <span id="matchGameStatus" class="text-neonCyan font-bold">1 / 5 세트 창고</span>
                </div>
                <div class="text-[10px] text-gray-400 leading-tight">
                    💡 각 라운드 입찰가는 <strong class="text-neonGold">라운드 종료 후 공개</strong>되며 <strong class="text-neonCyan">2.7배 이상</strong> 제시 시 조기 낙찰!
                </div>
            </div>
        </aside>

        <!-- CENTER PANEL: Bidding Arena & Interactive Controls -->
        <section class="lg:col-span-6 flex flex-col gap-5">

            <div class="relative bg-cyberDark border border-cyberBorder rounded-2xl h-64 md:h-72 overflow-hidden flex flex-col justify-between p-5 shadow-2xl">
                <div class="flex justify-between items-center z-10">
                    <div class="flex items-center gap-2 bg-cyberCard/90 border border-cyberBorder px-3.5 py-1.5 rounded-xl">
                        <span class="w-2.5 h-2.5 rounded-full bg-neonCyan animate-ping"></span>
                        <span id="subRoundBadge" class="text-xs font-bold text-white">라운드: 1 / 5</span>
                    </div>
                    <div id="turnIndicator" class="bg-neonPurple/20 border border-neonPurple/50 px-3.5 py-1.5 rounded-xl text-xs font-bold text-neonPurple">
                        비밀 입찰 진행 중
                    </div>
                </div>

                <!-- Warehouse Vault Animation Container -->
                <div class="absolute inset-0 flex z-0">
                    <div id="doorLeft" class="w-1/2 h-full bg-gradient-to-r from-gray-950 to-slate-900 border-r border-cyberBorder flex items-center justify-end pr-6 transition-transform duration-700">
                        <i class="fa-solid fa-warehouse text-7xl text-gray-800/40"></i>
                    </div>
                    <div id="doorRight" class="w-1/2 h-full bg-gradient-to-l from-gray-950 to-slate-900 border-l border-cyberBorder flex items-center justify-start pl-6 transition-transform duration-700">
                        <i class="fa-solid fa-warehouse text-7xl text-gray-800/40"></i>
                    </div>
                    
                    <div id="doorLock" class="absolute inset-0 flex flex-col items-center justify-center transition-opacity duration-500">
                        <div class="w-20 h-20 rounded-full bg-cyberDark/90 border-2 border-neonCyan flex items-center justify-center text-neonCyan shadow-2xl glow-cyan mb-2">
                            <i class="fa-solid fa-box-archive text-3xl"></i>
                        </div>
                        <span id="warehouseNameTag" class="text-xs font-bold tracking-widest text-gray-400 bg-cyberDark/90 px-4 py-1 rounded-full border border-cyberBorder">
                            봉인된 C-01 창고
                        </span>
                    </div>
                </div>

                <!-- Hammer Strike Overlay -->
                <div id="hammerContainer" class="hidden absolute inset-0 z-30 flex flex-col items-center justify-center bg-black/85 backdrop-blur-sm">
                    <div class="animate-hammer text-neonGold text-7xl mb-3">
                        <i class="fa-solid fa-gavel"></i>
                    </div>
                    <div id="hammerText" class="text-4xl font-black text-white text-glow-gold tracking-widest">
                        땅! 땅! 땅!
                    </div>
                    <div id="hammerSubtext" class="text-sm font-bold text-neonCyan mt-2">
                        낙찰 완료!
                    </div>
                </div>

                <div class="z-10 bg-cyberCard/90 backdrop-blur-md border border-cyberBorder p-3 rounded-xl flex items-center justify-between shadow-lg">
                    <div>
                        <div class="text-[11px] text-gray-400">현재 최고 확정 입찰가</div>
                        <div id="currentHighestBidText" class="text-2xl md:text-3xl font-black text-neonGold text-glow-gold">₩50,000</div>
                    </div>
                    <div class="text-right">
                        <div class="text-[11px] text-gray-400">최고 입찰 제시자</div>
                        <div id="highestBidderName" class="text-xs md:text-sm font-bold text-neonCyan">주최자</div>
                    </div>
                </div>
            </div>

            <!-- Feed Log Area -->
            <div class="bg-cyberDark/90 border border-cyberBorder rounded-xl p-3.5 h-32 overflow-y-auto text-xs space-y-1.5" id="auctionLogFeed">
                <div class="text-gray-400">🔒 1라운드 비밀 입찰이 진행 중입니다. 모든 플레이어의 입찰가는 라운드 종료 시 공개됩니다.</div>
            </div>

            <!-- Action Control Panel -->
            <div class="bg-cyberCard border border-cyberBorder p-4 rounded-2xl flex flex-col gap-3 shadow-xl">
                <div class="flex flex-wrap items-center justify-between gap-2">
                    <div class="flex flex-wrap items-center gap-1.5">
                        <button onclick="addBidAmount(10000)" class="bg-cyberDark hover:bg-cyberBorder border border-cyberBorder text-white px-3 py-1.5 rounded-lg text-xs font-bold transition">
                            +1만원
                        </button>
                        <button onclick="addBidAmount(50000)" class="bg-cyberDark hover:bg-cyberBorder border border-cyberBorder text-white px-3 py-1.5 rounded-lg text-xs font-bold transition">
                            +5만원
                        </button>
                        <button onclick="addBidAmount(100000)" class="bg-cyberDark hover:bg-cyberBorder border border-cyberBorder text-white px-3 py-1.5 rounded-lg text-xs font-bold transition">
                            +10만원
                        </button>
                        <button onclick="setMaxBidAmount()" class="bg-neonGold/20 hover:bg-neonGold/30 border border-neonGold/60 text-neonGold px-3 py-1.5 rounded-lg text-xs font-bold transition">
                            💎 Max 100만원
                        </button>
                        <button onclick="trigger2p7xBid()" class="bg-purple-900/40 hover:bg-purple-800 border border-neonPurple text-neonCyan px-3 py-1.5 rounded-lg text-xs font-bold transition">
                            🔥 2.7배 지르기
                        </button>
                    </div>

                    <button onclick="resetDraftInput()" title="입력 금액 0원 초기화" class="text-xs bg-red-950/60 hover:bg-red-900 text-red-300 px-3 py-1.5 rounded-lg border border-red-800 transition flex items-center gap-1 font-bold">
                        <i class="fa-solid fa-rotate-left"></i> 되돌리기 (0원)
                    </button>
                </div>

                <div class="flex items-center gap-2">
                    <div class="relative flex-1">
                        <span class="absolute left-3.5 top-2.5 text-xs text-neonGold font-bold">₩</span>
                        <input type="number" id="customBidInput" oninput="onCustomInputChange()" step="10000" min="0" max="1000000" placeholder="비밀 입찰가 입력" class="w-full bg-cyberDark border border-cyberBorder focus:border-neonCyan rounded-xl pl-8 pr-3 py-2 text-sm text-white font-bold outline-none transition">
                    </div>
                    
                    <button id="foldBtn" onclick="playerPass()" class="px-4 py-2.5 rounded-xl bg-gray-800 hover:bg-gray-700 text-gray-300 font-bold text-xs border border-gray-600 transition">
                        기권 (FOLD)
                    </button>

                    <button id="bidBtn" onclick="playerSubmitBid()" class="px-5 py-2.5 rounded-xl bg-gradient-to-r from-neonCyan to-blue-600 hover:from-cyan-400 hover:to-blue-500 text-black font-extrabold text-xs shadow-lg glow-cyan transition flex items-center gap-1.5">
                        <i class="fa-solid fa-user-lock"></i> <span id="bidBtnText">비밀 입찰 제출</span>
                    </button>
                </div>
            </div>

        </section>

        <!-- RIGHT PANEL: Player Status Cards -->
        <aside class="lg:col-span-3 flex flex-col gap-3">
            <h2 class="text-xs font-bold text-gray-300 flex items-center justify-between border-b border-cyberBorder pb-2">
                <span>참가자 현황 (4인)</span>
                <span class="text-[10px] text-gray-400">비밀 입찰 진행 중</span>
            </h2>

            <div class="space-y-2.5">
                <!-- Player 1 (나) -->
                <div id="pCard-0" class="bg-cyberCard border-2 border-neonCyan p-3 rounded-xl flex items-center space-x-3 transition-all">
                    <div class="relative">
                        <div class="w-9 h-9 rounded-lg bg-neonCyan/20 border border-neonCyan flex items-center justify-center text-neonCyan font-bold text-sm">
                            <i class="fa-solid fa-user-astronaut"></i>
                        </div>
                        <span id="pBadge-0" class="absolute -bottom-1 -right-1 w-2.5 h-2.5 rounded-full bg-green-500 border border-cyberDark"></span>
                    </div>
                    <div class="flex-1 min-w-0">
                        <div class="flex justify-between items-center">
                            <h4 class="text-xs font-bold text-white truncate">Player 1 (나)</h4>
                            <span id="pState-0" class="text-[9px] px-1.5 py-0.5 rounded bg-green-900/40 text-green-400 border border-green-800">온라인</span>
                        </div>
                        <div class="flex justify-between items-center mt-0.5">
                            <span id="pMoney-0" class="text-xs font-bold text-neonGold">₩1,000,000</span>
                            <span id="pLastBid-0" class="text-[10px] text-gray-400">🔒 비밀 제출 대기</span>
                        </div>
                    </div>
                </div>

                <!-- Player 2 -->
                <div id="pCard-1" class="bg-cyberCard border border-cyberBorder p-3 rounded-xl flex items-center space-x-3 transition-all">
                    <div class="relative">
                        <div class="w-9 h-9 rounded-lg bg-pink-500/20 border border-pink-500/50 flex items-center justify-center text-pink-400 font-bold text-sm">
                            <i class="fa-solid fa-user-ninja"></i>
                        </div>
                        <span id="pBadge-1" class="absolute -bottom-1 -right-1 w-2.5 h-2.5 rounded-full bg-green-500 border border-cyberDark"></span>
                    </div>
                    <div class="flex-1 min-w-0">
                        <div class="flex justify-between items-center">
                            <h4 class="text-xs font-bold text-white truncate">Player 2</h4>
                            <span id="pState-1" class="text-[9px] px-1.5 py-0.5 rounded bg-green-900/40 text-green-400 border border-green-800">온라인</span>
                        </div>
                        <div class="flex justify-between items-center mt-0.5">
                            <span id="pMoney-1" class="text-xs font-bold text-neonGold">₩1,000,000</span>
                            <span id="pLastBid-1" class="text-[10px] text-gray-400">🔒 비밀 제출 대기</span>
                        </div>
                    </div>
                </div>

                <!-- Player 3 -->
                <div id="pCard-2" class="bg-cyberCard border border-cyberBorder p-3 rounded-xl flex items-center space-x-3 transition-all">
                    <div class="relative">
                        <div class="w-9 h-9 rounded-lg bg-blue-500/20 border border-blue-500/50 flex items-center justify-center text-blue-400 font-bold text-sm">
                            <i class="fa-solid fa-user-gear"></i>
                        </div>
                        <span id="pBadge-2" class="absolute -bottom-1 -right-1 w-2.5 h-2.5 rounded-full bg-green-500 border border-cyberDark"></span>
                    </div>
                    <div class="flex-1 min-w-0">
                        <div class="flex justify-between items-center">
                            <h4 class="text-xs font-bold text-white truncate">Player 3</h4>
                            <span id="pState-2" class="text-[9px] px-1.5 py-0.5 rounded bg-green-900/40 text-green-400 border border-green-800">온라인</span>
                        </div>
                        <div class="flex justify-between items-center mt-0.5">
                            <span id="pMoney-2" class="text-xs font-bold text-neonGold">₩1,000,000</span>
                            <span id="pLastBid-2" class="text-[10px] text-gray-400">🔒 비밀 제출 대기</span>
                        </div>
                    </div>
                </div>

                <!-- Player 4 -->
                <div id="pCard-3" class="bg-cyberCard border border-cyberBorder p-3 rounded-xl flex items-center space-x-3 transition-all">
                    <div class="relative">
                        <div class="w-9 h-9 rounded-lg bg-purple-500/20 border border-purple-500/50 flex items-center justify-center text-purple-400 font-bold text-sm">
                            <i class="fa-solid fa-user-secret"></i>
                        </div>
                        <span id="pBadge-3" class="absolute -bottom-1 -right-1 w-2.5 h-2.5 rounded-full bg-green-500 border border-cyberDark"></span>
                    </div>
                    <div class="flex-1 min-w-0">
                        <div class="flex justify-between items-center">
                            <h4 class="text-xs font-bold text-white truncate">Player 4</h4>
                            <span id="pState-3" class="text-[9px] px-1.5 py-0.5 rounded bg-green-900/40 text-green-400 border border-green-800">온라인</span>
                        </div>
                        <div class="flex justify-between items-center mt-0.5">
                            <span id="pMoney-3" class="text-xs font-bold text-neonGold">₩1,000,000</span>
                            <span id="pLastBid-3" class="text-[10px] text-gray-400">🔒 비밀 제출 대기</span>
                        </div>
                    </div>
                </div>
            </div>
        </aside>
    </main>

    <!-- RESULT MODAL -->
    <div id="resultModal" class="hidden fixed inset-0 z-50 bg-black/85 backdrop-blur-md flex items-center justify-center p-4">
        <div class="bg-cyberCard border-2 border-cyberBorder rounded-2xl max-w-xl w-full p-6 shadow-2xl relative overflow-hidden max-h-[90vh] flex flex-col justify-between">
            <div>
                <div class="flex justify-between items-center pb-3 border-b border-cyberBorder">
                    <div>
                        <span id="modalResultBadge" class="text-[10px] font-bold px-2 py-0.5 rounded bg-neonCyan/20 text-neonCyan border border-neonCyan/40">
                            경매 정산 결과
                        </span>
                        <h2 id="modalWinnerTitle" class="text-xl font-black text-white mt-1">
                            Player 1 (나) 최종 낙찰!
                        </h2>
                    </div>
                    <div class="text-right">
                        <span class="text-[10px] text-gray-400">최종 낙찰 금액</span>
                        <div id="modalWinningBid" class="text-lg font-extrabold text-neonGold">₩450,000</div>
                    </div>
                </div>

                <div class="mt-4">
                    <h3 class="text-xs font-bold text-gray-400 uppercase tracking-wider mb-2 flex justify-between">
                        <span>창고 실제 소장품 공개 (최대 100만 가치)</span>
                        <span id="modalItemCount" class="text-neonCyan">총 4개 품목</span>
                    </h3>
                    <div id="itemListContainer" class="space-y-2 max-h-40 overflow-y-auto pr-1">
                        <!-- Dynamic Item List -->
                    </div>
                </div>

                <div id="summaryBox" class="mt-4 p-3.5 rounded-xl border bg-cyberDark flex flex-col gap-1.5">
                    <div class="flex justify-between text-xs">
                        <span class="text-gray-400">창고 실제 총 가치:</span>
                        <span id="modalTotalValue" class="font-bold text-white">₩520,000</span>
                    </div>
                    <div class="flex justify-between text-xs">
                        <span class="text-gray-400">낙찰 가격:</span>
                        <span id="modalWinningBidSub" class="font-bold text-neonPink">-₩450,000</span>
                    </div>
                    <div class="h-px bg-cyberBorder my-0.5"></div>
                    <div class="flex justify-between text-sm font-black">
                        <span>최종 손익 정산:</span>
                        <span id="modalNetProfit" class="text-neonCyan">+₩70,000</span>
                    </div>
                    <div id="modalBluffEvaluation" class="text-xs mt-1 text-gray-300 leading-normal bg-cyberCard p-2 rounded border border-cyberBorder">
                        <!-- Evaluation breakdown -->
                    </div>
                </div>
            </div>

            <div class="mt-5 pt-3 border-t border-cyberBorder flex justify-end">
                <button id="nextRoundBtn" onclick="nextWarehouseSet()" class="w-full sm:w-auto px-6 py-2.5 rounded-xl bg-gradient-to-r from-neonCyan to-neonPurple hover:opacity-90 text-black font-extrabold text-xs shadow-lg transition">
                    다음 창고 진행하기 <i class="fa-solid fa-arrow-right ml-1"></i>
                </button>
            </div>
        </div>
    </div>

    <!-- MATCH OVER SUMMARY MODAL -->
    <div id="matchSummaryModal" class="hidden fixed inset-0 z-50 bg-black/90 backdrop-blur-lg flex items-center justify-center p-4">
        <div class="bg-cyberCard border-2 border-neonGold rounded-2xl max-w-md w-full p-6 shadow-2xl relative overflow-hidden">
            <div class="text-center pb-4 border-b border-cyberBorder">
                <div class="w-14 h-14 rounded-full bg-neonGold/20 border-2 border-neonGold text-neonGold flex items-center justify-center mx-auto text-2xl mb-2 glow-gold">
                    <i class="fa-solid fa-trophy"></i>
                </div>
                <h2 class="text-xl font-black text-white">5 세트 최종 매치 종료!</h2>
                <p class="text-xs text-gray-400 mt-0.5">이환 시티 창고 경매 종합 성과 보고서</p>
            </div>

            <div class="mt-4 space-y-2.5">
                <div class="flex justify-between items-center bg-cyberDark p-3 rounded-xl border border-cyberBorder text-xs">
                    <span class="text-gray-400">최종 남은 소지금:</span>
                    <span id="finalPlayerMoney" class="text-base font-extrabold text-neonGold">₩1,000,000</span>
                </div>
                <div class="flex justify-between items-center bg-cyberDark p-3 rounded-xl border border-cyberBorder text-xs">
                    <span class="text-gray-400">이번 매치 누적 손익:</span>
                    <span id="finalMatchProfit" class="text-base font-extrabold text-neonCyan">₩0</span>
                </div>
                <div class="flex justify-between items-center bg-cyberDark p-3 rounded-xl border border-cyberBorder text-xs">
                    <span class="text-gray-400">경매사 평가 랭크:</span>
                    <span id="finalRankGrade" class="text-xs font-bold text-neonPurple">S급 경매 마스터</span>
                </div>
            </div>

            <div class="mt-5 pt-3 border-t border-cyberBorder">
                <button onclick="resetFullMatch()" class="w-full py-3 rounded-xl bg-gradient-to-r from-neonCyan via-blue-500 to-neonPurple hover:opacity-90 text-black font-extrabold text-xs shadow-lg transition glow-cyan">
                    새 매치 시작하기 (5세트 리셋)
                </button>
            </div>
        </div>
    </div>

    <script>
        let matchGame = 1; 
        const TOTAL_MATCH_GAMES = 5;

        let subRound = 1; 
        const MAX_SUB_ROUNDS = 5;

        let currentHighestBid = 50000;
        let currentHighestBidderCodes = ["주최자"]; 

        let playerTargetBid = 0;
        let isAuctionActive = true;
        let isRoundRevealing = false;

        const players = [
            { id: 0, name: 'Player 1 (나)', isHuman: true, money: 1000000, offline: false, folded: false, secretBid: null, submittedThisRound: false },
            { id: 1, name: 'Player 2', isHuman: false, type: 'aggressive', money: 1000000, offline: false, folded: false, secretBid: null, submittedThisRound: false },
            { id: 2, name: 'Player 3', isHuman: false, type: 'conservative', money: 1000000, offline: false, folded: false, secretBid: null, submittedThisRound: false },
            { id: 3, name: 'Player 4', isHuman: false, type: 'bluffer', money: 1000000, offline: false, folded: false, secretBid: null, submittedThisRound: false }
        ];

        let totalMatchProfit = 0;
        let setAIForceCounter = 0;

        let currentStorageItems = [];
        let currentTrueValue = 0;
        let currentRumors = [];
        let estimatedRange = { min: 0, max: 0 };
        let forceAIWinThisRound = false;

        const ITEM_DATABASE = [
            { name: "녹슨 철제 키링", category: "잡동사니", value: 10000, icon: "fa-key" },
            { name: "오래된 목재 의자", category: "가구", value: 30000, icon: "fa-chair" },
            { name: "레트로 픽셀 라디오", category: "전자가전", value: 50000, icon: "fa-radio" },
            { name: "중고 만화책 컬렉션", category: "서적", value: 40000, icon: "fa-book" },
            
            { name: "서브컬처 한정 피규어", category: "수집품", value: 120000, icon: "fa-robot" },
            { name: "튜닝용 아케이드 메인보드", category: "부품", value: 180000, icon: "fa-microchip" },
            { name: "고급 네온 탁상 조명", category: "인테리어", value: 110000, icon: "fa-lightbulb" },

            { name: "한정판 애니메이션 원화 초안", category: "희귀보물", value: 220000, icon: "fa-gem" },
            { name: "이환 시티 하이엔드 헤드셋", category: "음향기기", value: 200000, icon: "fa-headphones" },
            { name: "[특별 클라이언트 매입] 암호화 커널 칩", category: "특수요청", value: 250000, icon: "fa-crown" }
        ];

        const RUMOR_TEMPLATES = [
            { text: "- 비싼 물건이 많다는 소식", highChance: true },
            { text: "- 특별 클라이언트가 필요한 품목 존재", highChance: true },
            { text: "- 회사가 원하는 전자가전 수집 정보", highChance: true },
            { text: "- 방치된 낡은 가구 및 잡동사니 위주 소문", highChance: false },
            { text: "- 전 주인의 무난한 이삿짐 상자라는 정보", highChance: false }
        ];

        window.onload = function() {
            startRound();
        };

        function startRound() {
            subRound = 1;
            currentHighestBid = 50000;
            currentHighestBidderCodes = ["주최자"];
            playerTargetBid = 0;
            isAuctionActive = true;
            isRoundRevealing = false;

            setAIForceCounter++;
            forceAIWinThisRound = (setAIForceCounter % 3 === 0);

            document.getElementById('resultModal').classList.add('hidden');
            document.getElementById('matchSummaryModal').classList.add('hidden');
            document.getElementById('hammerContainer').classList.add('hidden');

            document.getElementById('doorLeft').classList.remove('door-left-open');
            document.getElementById('doorRight').classList.remove('door-right-open');
            document.getElementById('doorLock').style.opacity = '1';
            document.getElementById('warehouseNameTag').innerText = `봉인된 C-0${matchGame} 창고`;

            document.getElementById('auctionLogFeed').innerHTML = '';

            players.forEach(p => {
                p.folded = false;
                p.secretBid = null;
                p.submittedThisRound = false;
                if (p.money <= 0) p.offline = true;
            });

            generateStorageAndRumors();
            updateUIDashboard();
            addLog(`🔔 <strong>[창고 #${matchGame}]</strong> 1라운드 비밀 입찰이 시작되었습니다. 모든 플레이어가 입찰을 완료하면 일괄 공개됩니다.`);
        }

        function generateStorageAndRumors() {
            currentStorageItems = [];
            currentTrueValue = 0;

            const shuffled = [...RUMOR_TEMPLATES].sort(() => 0.5 - Math.random());
            currentRumors = shuffled.slice(0, 3);

            const itemCount = Math.floor(Math.random() * 3) + 3;
            let hasHighValueBoost = currentRumors.some(r => r.highChance);
            
            for (let i = 0; i < itemCount; i++) {
                let pool = ITEM_DATABASE;
                if (!hasHighValueBoost) {
                    pool = ITEM_DATABASE.filter(item => item.value <= 100000);
                }
                const randomItem = pool[Math.floor(Math.random() * pool.length)];
                
                if (currentTrueValue + randomItem.value <= 1000000) {
                    currentStorageItems.push(randomItem);
                    currentTrueValue += randomItem.value;
                }
            }

            const margin = Math.floor(currentTrueValue * 0.2);
            const minEst = Math.max(50000, currentTrueValue - margin);
            const maxEst = Math.min(1000000, currentTrueValue + margin);
            
            estimatedRange = { 
                min: Math.floor(minEst / 10000) * 10000, 
                max: Math.floor(maxEst / 10000) * 10000 
            };

            renderRumorSidebar();
        }

        function renderRumorSidebar() {
            const rumorListEl = document.getElementById('rumorList');
            rumorListEl.innerHTML = '';

            currentRumors.forEach(r => {
                const li = document.createElement('li');
                li.className = 'bg-cyberDark/80 p-1.5 rounded-lg border border-cyberBorder/80 text-gray-300 flex items-start gap-1.5';
                li.innerHTML = `<i class="fa-solid fa-angle-right text-neonCyan mt-0.5"></i> <span>${r.text}</span>`;
                rumorListEl.appendChild(li);
            });

            document.getElementById('estimatedRangeText').innerText = `₩${estimatedRange.min.toLocaleString()} ~ ₩${estimatedRange.max.toLocaleString()}`;
        }

        function updateUIDashboard() {
            document.getElementById('playerMoneyDisplay').innerText = `₩${players[0].money.toLocaleString()}`;
            document.getElementById('totalProfitDisplay').innerText = `${totalMatchProfit >= 0 ? '+' : ''}₩${totalMatchProfit.toLocaleString()}`;
            
            document.getElementById('matchGameStatus').innerText = `${matchGame} / ${TOTAL_MATCH_GAMES} 세트 창고`;
            document.getElementById('subRoundBadge').innerText = `라운드: ${subRound} / 5`;

            document.getElementById('turnIndicator').innerText = isRoundRevealing ? "라운드 공개 진행 중" : "비밀 입찰 진행 중";

            const highestBidEl = document.getElementById('currentHighestBidText');
            highestBidEl.innerText = `₩${currentHighestBid.toLocaleString()}`;

            document.getElementById('highestBidderName').innerText = getBidderNamesDisplay();

            const customInput = document.getElementById('customBidInput');
            if (document.activeElement !== customInput) {
                customInput.value = playerTargetBid;
            }

            document.getElementById('bidBtnText').innerText = `₩${playerTargetBid.toLocaleString()} 비밀 제출`;

            const foldBtn = document.getElementById('foldBtn');
            const bidBtn = document.getElementById('bidBtn');
            
            const canSubmit = isAuctionActive && !isRoundRevealing && !players[0].folded && !players[0].offline && !players[0].submittedThisRound;

            if (!canSubmit) {
                foldBtn.disabled = true;
                bidBtn.disabled = true;
                foldBtn.classList.add('opacity-40');
                bidBtn.classList.add('opacity-40');
            } else {
                foldBtn.disabled = false;
                bidBtn.disabled = false;
                foldBtn.classList.remove('opacity-40');
                bidBtn.classList.remove('opacity-40');
            }

            players.forEach((p, idx) => {
                const card = document.getElementById(`pCard-${idx}`);
                const badge = document.getElementById(`pBadge-${idx}`);
                const stateText = document.getElementById(`pState-${idx}`);
                const moneyText = document.getElementById(`pMoney-${idx}`);
                const lastBidText = document.getElementById(`pLastBid-${idx}`);

                moneyText.innerText = `₩${p.money.toLocaleString()}`;

                if (p.offline) {
                    card.className = "bg-cyberCard/40 border border-red-900/40 p-3 rounded-xl flex items-center space-x-3 opacity-40";
                    badge.className = "absolute -bottom-1 -right-1 w-2.5 h-2.5 rounded-full bg-red-600 border border-cyberDark";
                    stateText.innerText = "오프라인";
                    stateText.className = "text-[9px] px-1.5 py-0.5 rounded bg-red-950 text-red-400 border border-red-900";
                    lastBidText.innerText = "오프라인";
                } else if (p.folded) {
                    card.className = "bg-cyberCard border border-cyberBorder p-3 rounded-xl flex items-center space-x-3 opacity-50";
                    badge.className = "absolute -bottom-1 -right-1 w-2.5 h-2.5 rounded-full bg-gray-500 border border-cyberDark";
                    stateText.innerText = "기권(FOLD)";
                    stateText.className = "text-[9px] px-1.5 py-0.5 rounded bg-gray-800 text-gray-400";
                    lastBidText.innerText = "기권";
                } else {
                    const isHighest = currentHighestBidderCodes.includes(`Player-${idx}`);

                    if (isHighest) {
                        card.className = "bg-cyberCard border-2 border-neonGold p-3 rounded-xl flex items-center space-x-3 shadow-lg";
                    } else {
                        card.className = "bg-cyberCard border border-cyberBorder p-3 rounded-xl flex items-center space-x-3";
                    }

                    badge.className = "absolute -bottom-1 -right-1 w-2.5 h-2.5 rounded-full bg-green-500 border border-cyberDark";
                    
                    if (isHighest) {
                        stateText.innerText = "최고 입찰";
                        stateText.className = "text-[9px] px-1.5 py-0.5 rounded bg-neonGold/20 text-neonGold border border-neonGold/40 font-bold";
                    } else {
                        stateText.innerText = "온라인";
                        stateText.className = "text-[9px] px-1.5 py-0.5 rounded bg-green-900/40 text-green-400 border border-green-800";
                    }

                    if (p.submittedThisRound) {
                        if (isRoundRevealing) {
                            lastBidText.innerText = p.secretBid !== null ? `₩${p.secretBid.toLocaleString()}` : "기권";
                        } else {
                            lastBidText.innerText = p.id === 0 ? `₩${p.secretBid.toLocaleString()} (제출완료)` : "🔒 비밀 제출 완료";
                        }
                    } else {
                        lastBidText.innerText = "🔒 비밀 제출 대기";
                    }
                }
            });
        }

        function getBidderNamesDisplay() {
            if (currentHighestBidderCodes.length === 0) return "없음";
            if (currentHighestBidderCodes.includes("주최자")) return "주최자";

            const names = currentHighestBidderCodes.map(code => {
                if (code === 'Player-0') return 'Player 1 (나)';
                const idx = parseInt(code.split('-')[1]);
                return players[idx] ? players[idx].name : code;
            });

            return names.join(', ');
        }

        function addLog(msg) {
            const feed = document.getElementById('auctionLogFeed');
            const item = document.createElement('div');
            item.className = "text-gray-300";
            item.innerHTML = msg;
            feed.appendChild(item);
            feed.scrollTop = feed.scrollHeight;
        }

        function addBidAmount(amount) {
            if (!isAuctionActive || isRoundRevealing || players[0].submittedThisRound) return;
            playerTargetBid = Math.min(1000000, playerTargetBid + amount);
            updateUIDashboard();
        }

        function setMaxBidAmount() {
            if (!isAuctionActive || isRoundRevealing || players[0].submittedThisRound) return;
            playerTargetBid = 1000000;
            updateUIDashboard();
        }

        function trigger2p7xBid() {
            if (!isAuctionActive || isRoundRevealing || players[0].submittedThisRound) return;
            playerTargetBid = Math.min(1000000, Math.floor((currentHighestBid * 2.7) / 10000) * 10000);
            addLog("🔥 <strong>[2.7배 지르기 지정]</strong> 라운드 종료 후 공개 시 2.7배 조기 낙찰을 노립니다!");
            updateUIDashboard();
        }

        function resetDraftInput() {
            if (!isAuctionActive || isRoundRevealing || players[0].submittedThisRound) return;
            playerTargetBid = 0;
            document.getElementById('customBidInput').value = 0;
            addLog("🔄 입력창의 금액이 0원으로 초기화되었습니다. 다시 비밀 금액을 설정하세요.");
            updateUIDashboard();
        }

        function onCustomInputChange() {
            const val = parseInt(document.getElementById('customBidInput').value);
            if (!isNaN(val) && val >= 0 && val <= 1000000) {
                playerTargetBid = val;
                document.getElementById('bidBtnText').innerText = `₩${playerTargetBid.toLocaleString()} 비밀 제출`;
            }
        }

        function playerSubmitBid() {
            if (!isAuctionActive || isRoundRevealing || players[0].submittedThisRound) return;

            if (playerTargetBid > players[0].money) {
                addLog("❌ 보유금이 부족합니다!");
                return;
            }

            players[0].secretBid = playerTargetBid;
            players[0].submittedThisRound = true;
            addLog(`🔒 <strong>Player 1 (나)</strong> 님이 비밀 입찰을 제출했습니다.`);

            updateUIDashboard();
            processAIBindsAndCheckRoundEnd();
        }

        function playerPass() {
            if (!isAuctionActive || isRoundRevealing || players[0].submittedThisRound) return;

            players[0].folded = true;
            players[0].secretBid = null;
            players[0].submittedThisRound = true;
            addLog(`🏳️️ <strong>Player 1 (나)</strong> 님이 이번 경매를 기권(FOLD)했습니다.`);

            updateUIDashboard();
            processAIBindsAndCheckRoundEnd();
        }

        function processAIBindsAndCheckRoundEnd() {
            players.forEach(p => {
                if (!p.isHuman && !p.folded && !p.offline && !p.submittedThisRound) {
                    const estAvg = (estimatedRange.min + estimatedRange.max) / 2;
                    let maxWilling = estAvg;

                    if (forceAIWinThisRound) {
                        maxWilling = Math.max(currentHighestBid * 2.8, estimatedRange.max * 1.5);
                    } else {
                        if (p.type === 'aggressive') maxWilling = estimatedRange.max * 1.2;
                        else if (p.type === 'conservative') maxWilling = estAvg * 0.95;
                        else if (p.type === 'bluffer') maxWilling = estimatedRange.max * (0.8 + Math.random() * 0.4);
                    }

                    const can2p7x = (subRound <= 4 && currentHighestBid * 2.7 <= maxWilling && currentHighestBid * 2.7 <= p.money);

                    if (can2p7x && Math.random() > 0.4) {
                        p.secretBid = Math.min(1000000, Math.floor((currentHighestBid * 2.7) / 10000) * 10000);
                    } else {
                        const step = 10000 + Math.floor(Math.random() * 4) * 10000;
                        let nextBid = Math.min(1000000, currentHighestBid + step);

                        if (nextBid <= maxWilling && nextBid <= p.money) {
                            p.secretBid = nextBid;
                        } else {
                            p.folded = true;
                            p.secretBid = null;
                        }
                    }
                    p.submittedThisRound = true;
                }
            });

            isRoundRevealing = true;
            updateUIDashboard();
            addLog(`⏳ 모든 참가자의 비밀 제출이 완료되었습니다! 잠시 후 입찰 결과가 일괄 공개됩니다...`);

            setTimeout(revealRoundBids, 1500);
        }

        function revealRoundBids() {
            addLog(`📣 <strong>[${subRound}라운드 비밀 입찰 결과 공개!]</strong>`);

            let roundHighestBid = currentHighestBid;
            let roundHighestBidders = [...currentHighestBidderCodes];
            let is2p7xEarlyTriggered = false;

            const prevHighest = currentHighestBid;

            players.forEach(p => {
                if (!p.offline) {
                    if (p.folded || p.secretBid === null) {
                        addLog(`  • <strong>${p.name}</strong>: 기권 (FOLD)`);
                    } else {
                        addLog(`  • <strong>${p.name}</strong>: <span class="text-neonCyan font-bold">₩${p.secretBid.toLocaleString()}</span>`);
                        
                        if (subRound <= 4 && prevHighest > 0 && p.secretBid >= prevHighest * 2.7 && p.secretBid < 1000000) {
                            is2p7xEarlyTriggered = true;
                        }

                        if (p.secretBid === 1000000) {
                            if (roundHighestBid < 1000000) {
                                roundHighestBid = 1000000;
                                roundHighestBidders = [`Player-${p.id}`];
                            } else if (!roundHighestBidders.includes(`Player-${p.id}`)) {
                                roundHighestBidders.push(`Player-${p.id}`);
                            }
                        } else if (p.secretBid > roundHighestBid) {
                            roundHighestBid = p.secretBid;
                            roundHighestBidders = [`Player-${p.id}`];
                        }
                    }
                }
            });

            currentHighestBid = roundHighestBid;
            currentHighestBidderCodes = roundHighestBidders;

            updateUIDashboard();

            const activePlayers = players.filter(p => !p.folded && !p.offline);

            if (activePlayers.length <= 1) {
                addLog(`🔔 나머지 플레이어가 모두 포기하여 <strong>${getBidderNamesDisplay()}</strong> 님이 최종 낙찰받았습니다!`);
                setTimeout(finalizeAuctionWinner, 1500);
                return;
            }

            if (is2p7xEarlyTriggered) {
                addLog(`🔥 <strong>[2.7배 조기 낙찰 성립!]</strong> 이전 최고가 대비 2.7배 이상의 과감한 흥정이 성공하여 즉시 낙찰됩니다!`);
                setTimeout(finalizeAuctionWinner, 1500);
                return;
            }

            if (subRound < MAX_SUB_ROUNDS) {
                subRound++;
                isRoundRevealing = false;
                players.forEach(p => {
                    p.submittedThisRound = false;
                    p.secretBid = null;
                });

                playerTargetBid = 0;
                addLog(`🔄 <strong>[${subRound} / 5 라운드 시작]</strong> 다음 비밀 입찰을 준비하세요.`);
                
                // If human player passed, AI continues automatically
                if (players[0].folded || players[0].offline) {
                    setTimeout(processAIBindsAndCheckRoundEnd, 1000);
                } else {
                    updateUIDashboard();
                }
            } else {
                addLog(`🔔 5라운드 흥정 종료! 최고가 입찰자(들)가 최종 낙찰됩니다.`);
                setTimeout(finalizeAuctionWinner, 1500);
            }
        }

        function finalizeAuctionWinner() {
            isAuctionActive = false;

            const hammerOverlay = document.getElementById('hammerContainer');
            hammerOverlay.classList.remove('hidden');

            setTimeout(() => {
                document.getElementById('doorLeft').classList.add('door-left-open');
                document.getElementById('doorRight').classList.add('door-right-open');
                document.getElementById('doorLock').style.opacity = '0';
            }, 800);

            setTimeout(() => {
                showAuctionResultsModal();
            }, 2200);
        }

        function showAuctionResultsModal() {
            const modal = document.getElementById('resultModal');
            const winnerTitle = document.getElementById('modalWinnerTitle');
            const winnerBidText = document.getElementById('modalWinningBid');
            const winnerBidSubText = document.getElementById('modalWinningBidSub');
            const totalValueText = document.getElementById('modalTotalValue');
            const netProfitText = document.getElementById('modalNetProfit');
            const bluffEvalText = document.getElementById('modalBluffEvaluation');
            const itemListContainer = document.getElementById('itemListContainer');

            const isCoWinner = currentHighestBidderCodes.length > 1;
            const winnersDisplay = getBidderNamesDisplay();

            winnerTitle.innerText = isCoWinner ? `[공동 낙찰] ${winnersDisplay}` : `${winnersDisplay} 최종 낙찰!`;
            winnerBidText.innerText = `₩${currentHighestBid.toLocaleString()}`;
            winnerBidSubText.innerText = `-₩${currentHighestBid.toLocaleString()}`;
            totalValueText.innerText = `₩${currentTrueValue.toLocaleString()}`;

            const totalNetProfit = currentTrueValue - currentHighestBid;
            const winnerCount = currentHighestBidderCodes.length;

            const p0IsWinner = currentHighestBidderCodes.includes('Player-0');

            if (p0IsWinner) {
                if (totalNetProfit >= 0) {
                    const shareProfit = Math.floor(totalNetProfit / winnerCount);
                    players[0].money += shareProfit;
                    totalMatchProfit += shareProfit;

                    netProfitText.innerText = `+₩${shareProfit.toLocaleString()} (내 배분금)`;
                    netProfitText.className = "text-sm font-black text-neonCyan";

                    if (isCoWinner) {
                        bluffEvalText.innerHTML = `🤝 <strong>[100만원 공동 낙찰 이익 배분]</strong> 총 순익 <span class="text-neonCyan font-bold">₩${totalNetProfit.toLocaleString()}</span>을 ${winnerCount}명이 나눠 받아 각자 <span class="text-neonCyan font-bold">+₩${shareProfit.toLocaleString()}</span> 이득을 얻었습니다!`;
                    } else {
                        bluffEvalText.innerHTML = `🎉 <strong>[단독 흑자 성공]</strong> 창고를 이득 가격에 낙찰받아 <span class="text-neonCyan font-bold">+₩${shareProfit.toLocaleString()}</span> 의 순이익을 얻었습니다!`;
                    }
                } else {
                    const fullLoss = Math.abs(totalNetProfit);
                    players[0].money -= fullLoss;
                    totalMatchProfit -= fullLoss;

                    netProfitText.innerText = `-₩${fullLoss.toLocaleString()} (개별 차감)`;
                    netProfitText.className = "text-sm font-black text-neonPink";

                    if (isCoWinner) {
                        bluffEvalText.innerHTML = `⚠️ <strong>[공동 낙찰 손해 발생]</strong> 창고 실제 가치가 부족하여 공동 참여자 ${winnerCount}명 전원이 각자 <span class="text-neonPink font-bold">-₩${fullLoss.toLocaleString()}</span> 차감되었습니다!`;
                    } else {
                        bluffEvalText.innerHTML = `🚨 <strong>[적자 발생]</strong> 흥정이 과열되어 손해를 보았습니다: <span class="text-neonPink font-bold">-₩${fullLoss.toLocaleString()}</span>`;
                    }
                }
            } else {
                currentHighestBidderCodes.forEach(code => {
                    const idx = parseInt(code.split('-')[1]);
                    if (players[idx]) {
                        if (totalNetProfit >= 0) {
                            players[idx].money += Math.floor(totalNetProfit / winnerCount);
                        } else {
                            players[idx].money -= Math.abs(totalNetProfit);
                        }
                    }
                });

                if (totalNetProfit < 0) {
                    const aiLoss = Math.abs(totalNetProfit);
                    const compensation = Math.floor(aiLoss * 0.1);
                    
                    players[0].money += compensation;
                    totalMatchProfit += compensation;

                    netProfitText.innerText = `상대 손실: -₩${aiLoss.toLocaleString()}`;
                    netProfitText.className = "text-sm font-black text-neonGold";
                    bluffEvalText.innerHTML = `😈 <strong>[블러핑 성공!]</strong> 상대에게 적자 창고를 안겼습니다! 위자료로 <span class="text-neonCyan font-bold">+₩${compensation.toLocaleString()}</span> 보상금을 챙겼습니다!`;
                } else {
                    netProfitText.innerText = `상대 수익: +₩${totalNetProfit.toLocaleString()}`;
                    netProfitText.className = "text-sm font-black text-gray-400";
                    bluffEvalText.innerHTML = `🔍 ${winnersDisplay} 님이 창고를 획득하여 profit을 챙겼습니다.`;
                }
            }

            players.forEach(p => {
                if (p.money <= 0) {
                    p.money = 0;
                    p.offline = true;
                }
            });

            itemListContainer.innerHTML = '';
            document.getElementById('modalItemCount').innerText = `총 ${currentStorageItems.length}개 품목`;

            currentStorageItems.forEach(item => {
                const itemRow = document.createElement('div');
                itemRow.className = "flex justify-between items-center bg-cyberDark p-2.5 rounded-lg border border-cyberBorder text-xs";
                itemRow.innerHTML = `
                    <div class="flex items-center gap-2.5">
                        <div class="w-7 h-7 rounded bg-cyberCard border border-cyberBorder flex items-center justify-center text-neonCyan text-xs">
                            <i class="fa-solid ${item.icon}"></i>
                        </div>
                        <div>
                            <div class="font-bold text-white">${item.name}</div>
                            <div class="text-[10px] text-gray-400">${item.category}</div>
                        </div>
                    </div>
                    <div class="font-bold text-neonGold">
                        +₩${item.value.toLocaleString()}
                    </div>
                `;
                itemListContainer.appendChild(itemRow);
            });

            const nextRoundBtn = document.getElementById('nextRoundBtn');
            if (matchGame >= TOTAL_MATCH_GAMES) {
                nextRoundBtn.innerHTML = `최종 매치 성과 보고 <i class="fa-solid fa-trophy ml-1"></i>`;
            } else {
                nextRoundBtn.innerHTML = `다음 창고 진행하기 (${matchGame + 1}/5) <i class="fa-solid fa-arrow-right ml-1"></i>`;
            }

            modal.classList.remove('hidden');
            updateUIDashboard();
        }

        function nextWarehouseSet() {
            if (matchGame < TOTAL_MATCH_GAMES) {
                matchGame++;
                startRound();
            } else {
                document.getElementById('resultModal').classList.add('hidden');
                showMatchSummaryModal();
            }
        }

        function showMatchSummaryModal() {
            document.getElementById('finalPlayerMoney').innerText = `₩${players[0].money.toLocaleString()}`;
            document.getElementById('finalMatchProfit').innerText = `${totalMatchProfit >= 0 ? '+' : ''}₩${totalMatchProfit.toLocaleString()}`;

            let grade = "C급 초보 경매사";
            if (totalMatchProfit > 800000) grade = "S급 전설의 경매 대부";
            else if (totalMatchProfit > 300000) grade = "A급 이환 시티 베테랑";
            else if (totalMatchProfit > 0) grade = "B급 알뜰 수집가";

            document.getElementById('finalRankGrade').innerText = grade;
            document.getElementById('matchSummaryModal').classList.remove('hidden');
        }

        function resetFullMatch() {
            matchGame = 1;
            totalMatchProfit = 0;
            setAIForceCounter = 0;

            players.forEach((p) => {
                p.money = 1000000;
                p.offline = false;
            });

            startRound();
        }
    </script>
</body>
</html>
