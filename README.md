<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ホットペッパーグルメ検索</title>
</head>
<body>
    <h1>
        ホットペッパーグルメ検索
    </h1>
    <form id="search-form">
        <input type="text" id="keyword">
        <button type="submit">検索</button>
    </form>
    <div id="results">ここにJSONが表示される</div>
        Powered by <a href="http://webservice.recruit.co.jp/">ホットペッパーグルメ Webサービス</a>
    <script>
        const API_KEY="168d11c138b3673c"//定数
        const API_URL="http://webservice.recruit.co.jp/hotpepper/gourmet/v1/"//定数

        document.getElementById("search-form").addEventListener("submit", async (event) => {
            console.log("押されたよ");
            event.preventDefault();

            const keyword =document.getElementById("keyword").value;

            document.getElementById("results").innerHTML = keyword;
            //cors回避用のプロキシurl
            const proxyUrl = "https://api.allorigins.win/raw?url=";
            const url = `${proxyUrl}${encodeURIComponent(`${API_URL}?key=${API_KEY}&format=json&keyword=${encodeURIComponent(keyword)}&count=10`)}`;

            try{
                const response = await fetch(url);
                if(!response.ok) {
                    throw new Error(`HTTPエラー: ${response.status}`);
                }
                const data = await response.json();
                displayResults(data.results.shop);
            }
            catch(error) {
                document.getElementById("results").innerHTML = "データ取得に失敗しました";
            }
        });
        function displayResults(shops) {
            const resultsDiv = document.getElementById("results");
            if(shops && shops.length > 0) {
                shops.forEach(shop => {
                    const shopDiv = document.createElement("div");
                    shopDiv.innerHTML = `
                    <h2>${shop.name}</h2>
                    <p>住所 :${shop.address}</p>
                    <p><a href=${shop.urls.pc}" target="_blank">店舗情報を見る</a></p>
                    <img src="${shop.photo.pc.l}" alt="${shop.name}"/>
                    <hr>
                    `;
                    resultsDiv.appendChild(shopDiv);
                });
            } else {
                resultsDiv.innerHTML = "該当する店舗がありませんでした";
            }
        }
    </script>
</body>
</html>
