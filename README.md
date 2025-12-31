<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2026 时光解语者 (AI 真实版)</title>
    <style>
        /* 保持原有的星空样式，无需改动 */
        body { margin: 0; padding: 0; font-family: 'PingFang SC', sans-serif; background: linear-gradient(135deg, #0f0c29, #302b63, #24243e); color: #fff; min-height: 100vh; display: flex; justify-content: center; align-items: center; overflow-x: hidden; }
        .container { width: 90%; max-width: 500px; background: rgba(255, 255, 255, 0.1); backdrop-filter: blur(15px); -webkit-backdrop-filter: blur(15px); border-radius: 20px; border: 1px solid rgba(255, 255, 255, 0.2); padding: 40px 30px; box-shadow: 0 15px 35px rgba(0, 0, 0, 0.5); text-align: center; position: relative; }
        h1 { font-size: 24px; margin-bottom: 10px; background: linear-gradient(to right, #fff, #a8c0ff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        p.subtitle { font-size: 14px; color: rgba(255, 255, 255, 0.6); margin-bottom: 30px; }
        textarea { width: 100%; height: 120px; background: rgba(0, 0, 0, 0.3); border: 1px solid rgba(255, 255, 255, 0.1); border-radius: 12px; padding: 15px; color: #fff; font-size: 16px; resize: none; outline: none; box-sizing: border-box; }
        textarea:focus { border-color: #a8c0ff; }
        .btn-generate { background: linear-gradient(45deg, #6a11cb 0%, #2575fc 100%); border: none; padding: 12px 40px; border-radius: 50px; color: white; font-size: 16px; font-weight: bold; cursor: pointer; box-shadow: 0 5px 15px rgba(37, 117, 252, 0.4); margin-top: 20px;}
        .btn-generate:disabled { background: #555; cursor: not-allowed; }
        .result-card { display: none; margin-top: 30px; text-align: left; border-top: 1px solid rgba(255,255,255,0.1); padding-top: 20px; animation: fadeIn 1s ease; }
        .keyword-box { text-align: center; margin-bottom: 20px; }
        .keyword { font-size: 36px; font-weight: bold; color: #ffd700; text-shadow: 0 0 10px rgba(255, 215, 0, 0.5); padding: 5px 20px; border: 1px solid rgba(255, 215, 0, 0.3); border-radius: 8px; background: rgba(255, 215, 0, 0.05); }
        .section-title { font-size: 14px; color: #a8c0ff; margin-top: 15px; margin-bottom: 5px; }
        .section-content { font-size: 15px; line-height: 1.6; color: rgba(255, 255, 255, 0.9); margin-bottom: 10px; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .loader { display: none; margin: 20px auto; border: 3px solid rgba(255,255,255,0.1); border-radius: 50%; border-top: 3px solid #fff; width: 24px; height: 24px; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .bg-circle { position: absolute; border-radius: 50%; filter: blur(80px); z-index: -1; }
        .c1 { width: 300px; height: 300px; background: #7f00ff; top: -50px; left: -50px; opacity: 0.4; }
        .c2 { width: 400px; height: 400px; background: #00d2ff; bottom: -100px; right: -100px; opacity: 0.3; }
    </style>
</head>
<body>
    <div class="bg-circle c1"></div>
    <div class="bg-circle c2"></div>

    <div class="container">
        <h1>2026 时光解语者</h1>
        <p class="subtitle">AI 正在连接未来，为你解码 2026</p>
        <div class="input-group">
            <textarea id="userWish" placeholder="请告诉我你对 2026 年的真实愿望..."></textarea>
        </div>
        <button class="btn-generate" onclick="callAI()">开启启示</button>
        <div class="loader" id="loader"></div>
        <div class="result-card" id="resultCard">
            <div class="keyword-box"><span class="keyword" id="resultKeyword"></span></div>
            <div class="section-title">📖 解语</div>
            <div class="section-content" id="resultExplanation"></div>
            <div class="section-title">🎨 灵感画面</div>
            <div class="section-content" id="resultImageDesc"></div>
            <div class="section-title">💡 2026 行动指南</div>
            <div class="section-content" id="resultAction"></div>
        </div>
    </div>

    <script>
        // ==========================================
        // 🔴 重点：请在这里填入你的 API Key
        // 保留引号，只替换中间的文字
        // ==========================================
        const API_KEY = "你的_API_KEY_填在这里"; 
        // ==========================================

        async function callAI() {
            const wish = document.getElementById('userWish').value;
            const btn = document.querySelector('.btn-generate');
            const loader = document.getElementById('loader');
            const resultCard = document.getElementById('resultCard');

            if (!wish.trim()) { alert("请先写下你的愿望..."); return; }
            if (API_KEY === "你的_API_KEY_填在这里") { alert("请先在代码里填入你的 API Key！"); return; }

            // UI 切换为加载状态
            resultCard.style.display = 'none';
            btn.disabled = true;
            btn.textContent = "正在连接宇宙信号...";
            loader.style.display = 'block';

            // 准备 Prompt (提示词)
            const systemPrompt = `你是一位2026时光解语者。请根据用户愿望，输出一个JSON格式的回答。
            格式要求：
            {
                "keyword": "两个字的中文关键词",
                "explanation": "50字左右的温暖哲理解读",
                "image": "一句话描述灵感画面",
                "action": "一句简短具体的行动建议"
            }
            不要输出任何Markdown标记，只输出纯JSON字符串。`;

            try {
                // 发送请求给 DeepSeek
                const response = await fetch("https://api.deepseek.com/v1/chat/completions", {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": `Bearer ${API_KEY}`
                    },
                    body: JSON.stringify({
                        model: "deepseek-chat",
                        messages: [
                            {"role": "system", "content": systemPrompt},
                            {"role": "user", "content": wish}
                        ],
                        temperature: 1.3 // 稍微高一点的温度，让AI更有创意
                    })
                });

                const data = await response.json();
                
                if (data.error) {
                    throw new Error(data.error.message);
                }

                // 解析 AI 的回复
                let aiContent = data.choices[0].message.content;
                // 清理一下可能存在的 markdown 符号
                aiContent = aiContent.replace(/```json/g, "").replace(/```/g, "");
                const result = JSON.parse(aiContent);

                // 显示结果
                document.getElementById('resultKeyword').textContent = `【 ${result.keyword} 】`;
                document.getElementById('resultExplanation').textContent = result.explanation;
                document.getElementById('resultImageDesc').textContent = result.image;
                document.getElementById('resultAction').textContent = result.action;

                resultCard.style.display = 'block';

            } catch (error) {
                alert("连接未来失败了 (报错信息): " + error.message);
                console.error(error);
            } finally {
                loader.style.display = 'none';
                btn.textContent = "再次开启";
                btn.disabled = false;
            }
        }
    </script>
</body>
</html>
