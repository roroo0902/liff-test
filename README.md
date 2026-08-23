<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <title>照拿藥表單</title>

  <!-- LINE LIFF SDK -->
  <script src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
</head>

<body>

  <h1>正在登入...</h1>

  <script>

    document.addEventListener("DOMContentLoaded", async () => {

      try {

        console.log("📌 LIFF 初始化...");

        // ==============================
        // LIFF 初始化
        // ==============================

        await liff.init({
          liffId: "2011216294-Ug0co1AC"
        });


        // ==============================
        // 檢查 LINE 是否登入
        // ==============================

        if (!liff.isLoggedIn()) {

          console.log("🔹 尚未登入，執行 LINE 登入...");

          liff.login();

          return;

        }


        console.log("🔹 已登入，開始取得使用者資料...");

        await sendUserDataAndRedirect();


      } catch (err) {

        console.error(
          "❌ LIFF 初始化失敗:",
          err
        );

        redirectToGoogleForm();

      }

    });



    // ==================================================
    // 取得 LINE 使用者資料
    // ==================================================

    async function sendUserDataAndRedirect() {

      try {

        const profile =
          await liff.getProfile();


        console.log(
          "✅ LINE 使用者名稱:",
          profile.displayName
        );


        console.log(
          "✅ LINE User ID:",
          profile.userId
        );



        // ==================================================
        // 暫存 LINE 資料
        // ==================================================

        localStorage.setItem(
          "userName",
          profile.displayName
        );


        localStorage.setItem(
          "userId",
          profile.userId
        );



        // ==================================================
        // Google Apps Script
        // 將 LINE 名稱 + userId 寫入「使用者資料」
        // ==================================================

        const gasURL =
          "https://script.google.com/macros/s/AKfycbzYuqjj4MuS8MOcwZ4vmNGwLahX03TmXeKZIvF0IZ5Anqotl3K8818LVqm5M_3b7axlWg/exec";


        const url =
          `${gasURL}` +
          `?userName=${encodeURIComponent(profile.displayName)}` +
          `&userId=${encodeURIComponent(profile.userId)}`;


        await fetch(
          url,
          {
            mode: "no-cors"
          }
        );


        console.log(
          "✅ LINE 使用者資料已送至 Google Apps Script"
        );



        // ==================================================
        // Google Form
        // ==================================================

        const formBase =
          "https://docs.google.com/forms/d/e/1FAIpQLSeh5f7ZzfHBSDrI5XzpQBK8NS94B4thOr9B4gw1GgFppfW5xA/viewform?usp=header";


        // Google Form「姓名」欄位
        const entryName =
          "entry.1359086443";


        // ==================================================
        // 只將 LINE 名稱預填到 Google Form
        // userId 不會顯示在 Google Form
        // ==================================================

        const finalFormURL =
          `${formBase}` +
          `&${entryName}=${encodeURIComponent(profile.displayName)}`;


        console.log(
          "✅ 前往照拿藥表單:",
          finalFormURL
        );


        window.location.href =
          finalFormURL;


      } catch (err) {

        console.error(
          "❌ 使用者資料處理失敗:",
          err
        );


        redirectToGoogleForm();

      }

    }



    // ==================================================
    // 若 LINE / GAS 發生錯誤
    // 仍然讓患者可以進入 Google Form
    // ==================================================

    function redirectToGoogleForm() {

      console.log(
        "🔹 直接前往照拿藥表單（無預填）"
      );


      window.location.href =
        "https://docs.google.com/forms/d/e/1FAIpQLSeh5f7ZzfHBSDrI5XzpQBK8NS94B4thOr9B4gw1GgFppfW5xA/viewform?usp=header";

    }

  </script>

</body>
</html>
