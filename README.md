<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>COMPASSION WORLD 入店</title>
  <script src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
  <style>
    body{font-family:sans-serif;text-align:center;padding:40px;background:#f5f7fb}
    .card{max-width:520px;margin:40px auto;background:#fff;border-radius:20px;padding:28px;box-shadow:0 10px 30px rgba(0,0,0,.08)}
    .badge{display:inline-block;padding:6px 12px;border-radius:999px;background:#eef2ff;color:#4338ca;font-size:12px;margin-bottom:16px}
    .ok{color:#166534}.ng{color:#b91c1c}
  </style>
</head>
<body>
  <div class="card">
    <div class="badge">COMPASSION WORLD</div>
    <h2 id="msg">LINE認証を確認しています</h2>
    <p id="sub">少々お待ちください。</p>
  </div>

  <script>
    const LIFF_ID = "2008818718-Mq4qlTTs";
    const GAS_URL = "ここをGASのWebアプリURLに置き換える";

    async function main() {
      try {
        await liff.init({ liffId: LIFF_ID });

        if (!liff.isLoggedIn()) {
          document.getElementById("msg").innerText = "LINEアプリから開いてください";
          document.getElementById("sub").innerText = "この画面は LIFF URL から開く必要があります。";
          return;
        }

        const idToken = liff.getIDToken();
        if (!idToken) {
          document.getElementById("msg").innerText = "認証に失敗しました";
          document.getElementById("sub").innerText = "LIFFアプリの openid スコープを確認してください。";
          return;
        }

        const res = await fetch(GAS_URL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            action: "entry_check_line",
            idToken: idToken
          })
        });

        const data = await res.json();

        if (data.ok) {
          document.getElementById("msg").innerText = "確認できました。扉を開ける準備をしています。";
          document.getElementById("sub").innerText =
            (data.name ? `対象: ${data.name} / ` : "") +
            "扉が開きます。お足元にご注意の上、通行してください。";
        } else {
          document.getElementById("msg").innerText = "入店できません";
          document.getElementById("sub").innerText = data.reason || "不明な理由";
        }
      } catch (e) {
        document.getElementById("msg").innerText = "通信エラーが発生しました";
        document.getElementById("sub").innerText = String(e);
      }
    }

    main();
  </script>
</body>
</html>
