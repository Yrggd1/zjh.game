# zjh.game
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>炸金花游戏</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
        }
        .card {
            display: inline-block;
            margin: 10px;
            padding: 10px;
            border: 1px solid #000;
            border-radius: 5px;
            background-color: #f9f9f9;
        }
        button {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <h1>炸金花游戏</h1>
    <div id="player1-hand" class="hand">
        <h2>玩家1的手牌</h2>
        <div id="player1-cards" class="cards"></div>
    </div>
    <div id="player2-hand" class="hand">
        <h2>玩家2的手牌</h2>
        <div id="player2-cards" class="cards"></div>
    </div>
    <button onclick="dealCards()">发牌</button>
    <div id="result"></div>

    <script>
        const suits = ['♠', '♥', '♦', '♣'];
        const ranks = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A'];
        let deck = [];

        function createDeck() {
            deck = [];
            for (let suit of suits) {
                for (let rank of ranks) {
                    deck.push(rank + suit);
                }
            }
        }

        function shuffleDeck() {
            for (let i = deck.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [deck[i], deck[j]] = [deck[j], deck[i]];
            }
        }

        function dealCards() {
            createDeck();
            shuffleDeck();
            const player1Hand = deck.slice(0, 3);
            const player2Hand = deck.slice(3, 6);
            displayCards('player1-cards', player1Hand);
            displayCards('player2-cards', player2Hand);
            const result = compareHands(player1Hand, player2Hand);
            document.getElementById('result').innerText = result;
        }

        function displayCards(elementId, cards) {
            const cardsElement = document.getElementById(elementId);
            cardsElement.innerHTML = '';
            for (let card of cards) {
                const cardElement = document.createElement('div');
                cardElement.className = 'card';
                cardElement.innerText = card;
                cardsElement.appendChild(cardElement);
            }
        }

        function evaluateHand(hand) {
            const ranksCount = {};
            const suitsCount = {};
            for (let card of hand) {
                const rank = card.slice(0, -1);
                const suit = card.slice(-1);
                ranksCount[rank] = (ranksCount[rank] || 0) + 1;
                suitsCount[suit] = (suitsCount[suit] || 0) + 1;
            }

            const sortedRanks = Object.keys(ranksCount).sort((a, b) => ranks.indexOf(a) - ranks.indexOf(b));
            const isStraight = sortedRanks.length === 3 && ranks.indexOf(sortedRanks[2]) - ranks.indexOf(sortedRanks[0]) === 2;
            const isFlush = Object.keys(suitsCount).length === 1;
            const isThreeOfAKind = Object.keys(ranksCount).length === 1;
            const isPair = Object.keys(ranksCount).length === 2;

            if (isThreeOfAKind) return "豹子";
            if (isStraight && isFlush) return "同花顺";
            if (isFlush) return "同花";
            if (isStraight) return "顺子";
            if (isPair) return "对子";
            return "散牌";
        }

        function compareHands(hand1, hand2) {
            const rankOrder = ["散牌", "对子", "顺子", "同花", "同花顺", "豹子"];
            const hand1Type = evaluateHand(hand1);
            const hand2Type = evaluateHand(hand2);
            if (rankOrder.indexOf(hand1Type) > rankOrder.indexOf(hand2Type)) {
                return "玩家1胜";
            } else if (rankOrder.indexOf(hand1Type) < rankOrder.indexOf(hand2Type)) {
                return "玩家2胜";
            } else {
                return "平局";
            }
        }
    </script>
</body>
</html>
