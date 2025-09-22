<!DOCTYPE html>
<html>
<head>
    <title>Trading Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        .market {
            border: 1px solid #ccc;
            padding: 10px;
            margin-bottom: 10px;
        }
        button {
            margin-top: 5px;
        }
            /* Enhanced styles and animations */
            body {
                background: linear-gradient(120deg, #f8fafc 0%, #e0e7ff 100%);
                min-height: 100vh;
            }
            h1 {
                text-align: center;
                font-size: 2.5em;
                margin-bottom: 30px;
                color: #3b82f6;
                letter-spacing: 2px;
                animation: fadeInDown 1s;
            }
            @keyframes fadeInDown {
                from { opacity: 0; transform: translateY(-30px); }
                to { opacity: 1; transform: translateY(0); }
            }
            .market {
                border-radius: 12px;
                box-shadow: 0 2px 12px rgba(59,130,246,0.08);
                background: #fff;
                transition: box-shadow 0.3s;
                animation: fadeInUp 0.8s;
            }
            .market:hover {
                box-shadow: 0 4px 24px rgba(59,130,246,0.18);
            }
            @keyframes fadeInUp {
                from { opacity: 0; transform: translateY(30px); }
                to { opacity: 1; transform: translateY(0); }
            }
            button {
                background: #3b82f6;
                color: #fff;
                border: none;
                border-radius: 6px;
                padding: 8px 18px;
                font-size: 1em;
                cursor: pointer;
                margin-right: 8px;
                box-shadow: 0 1px 4px rgba(59,130,246,0.08);
                transition: background 0.2s, transform 0.2s;
            }
            button:active {
                background: #2563eb;
                transform: scale(0.97);
            }
            button:focus {
                outline: 2px solid #3b82f6;
            }
            #portfolio {
                font-size: 1.2em;
                color: #16a34a;
                font-weight: bold;
                margin-top: 10px;
                transition: color 0.4s;
            }
            .price-up {
                color: #16a34a;
                animation: priceUp 0.7s;
            }
            .price-down {
                color: #dc2626;
                animation: priceDown 0.7s;
            }
            @keyframes priceUp {
                0% { background: #bbf7d0; }
                100% { background: transparent; }
            }
            @keyframes priceDown {
                0% { background: #fecaca; }
                100% { background: transparent; }
            }
    </style>
</head>
<body>
    <h1>Trading Game</h1>

    <div id="markets">
        </div>

    <div>
        <h2>Portfolio</h2>
        <p id="portfolio">Cash: $1000</p>
    </div>

    <script>
        let cash = 1000;
        const marketsData = [
            { name: "Tech Stocks", basePrice: 100 },
            { name: "Crypto", basePrice: 50 },
            { name: "Commodities", basePrice: 200 }
        ];

        function createMarket(marketData) {
            const marketDiv = document.createElement("div");
            marketDiv.classList.add("market");

            const nameH2 = document.createElement("h2");
            nameH2.textContent = marketData.name;
            marketDiv.appendChild(nameH2);

                let currentPrice = marketData.basePrice;
                const priceP = document.createElement("p");
                priceP.textContent = `Current Price: $${currentPrice.toFixed(2)}`;
                priceP.classList.add("market-price");
                // Animate price change
                if (marketData.lastPrice !== undefined) {
                    if (currentPrice > marketData.lastPrice) {
                        priceP.classList.add("price-up");
                    } else if (currentPrice < marketData.lastPrice) {
                        priceP.classList.add("price-down");
                    }
                }
                marketDiv.appendChild(priceP);

            const buyButton = document.createElement("button");
            buyButton.textContent = `Buy ${marketData.name}`;
            buyButton.addEventListener("click", () => {
                buyAsset(marketData, currentPrice);
            });
            marketDiv.appendChild(buyButton);

             const sellButton = document.createElement("button");
            sellButton.textContent = `Sell ${marketData.name}`;
            sellButton.addEventListener("click", () => {
                sellAsset(marketData, currentPrice);
            });
            marketDiv.appendChild(sellButton);

            return marketDiv;
        }

        function buyAsset(marketData, currentPrice) {
            if (cash >= currentPrice) {
                cash -= currentPrice;
                updatePortfolio();
                alert(`Bought ${marketData.name} for $${currentPrice}`);
            } else {
                alert("Not enough cash!");
            }
        }

        function sellAsset(marketData, currentPrice) {
            cash += currentPrice;
            updatePortfolio();
            alert(`Sold ${marketData.name} for $${currentPrice}`);
        }

        function updatePortfolio() {
              const portfolio = document.getElementById("portfolio");
              portfolio.textContent = `Cash: $${cash.toFixed(2)}`;
              portfolio.style.color = cash >= 1000 ? "#16a34a" : (cash < 500 ? "#dc2626" : "#f59e42");
        }

        function updateMarketPrices() {
            const marketsDiv = document.getElementById("markets");
            marketsDiv.innerHTML = ""; 

                marketsData.forEach(marketData => {
                    const volatility = Math.random() * 0.2 - 0.1;
                    marketData.lastPrice = marketData.basePrice;
                    marketData.basePrice *= (1 + volatility);
                    const marketDiv = createMarket(marketData);
                    marketsDiv.appendChild(marketDiv);
                });
        }

        document.addEventListener("DOMContentLoaded", () => {
            updateMarketPrices();
            setInterval(updateMarketPrices, 5000);
        });
    </script>
</body>
</html>
