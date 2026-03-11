[mon site1 .html](https://github.com/user-attachments/files/25920097/mon.site1.html)
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chatbot n8n - Version Optimisée</title>
    <style>
        :root { --primary: #007bff; --bg: #f0f2f5; --bot-msg: #e4e6eb; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: var(--bg); display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
        #chat { width: 400px; height: 600px; background: white; border-radius: 12px; display: flex; flex-direction: column; box-shadow: 0 8px 24px rgba(0,0,0,0.15); overflow: hidden; }
        #messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; }
        .msg { padding: 10px 14px; border-radius: 18px; max-width: 80%; font-size: 14px; line-height: 1.4; word-wrap: break-word; }
        .user { background: var(--primary); color: white; align-self: flex-end; border-bottom-right-radius: 4px; }
        .bot { background: var(--bot-msg); color: #1c1e21; align-self: flex-start; border-bottom-left-radius: 4px; }
        .error { background: #ffebe9; color: #d73a49; border: 1px solid #ff8182; align-self: center; font-size: 12px; }
        #inputArea { display: flex; padding: 10px; border-top: 1px solid #eee; background: #fff; }
        input { flex: 1; border: 1px solid #ddd; padding: 12px; border-radius: 20px; outline: none; transition: border 0.2s; }
        input:focus { border-color: var(--primary); }
        button { background: var(--primary); color: white; border: none; padding: 0 15px; margin-left: 8px; border-radius: 50%; cursor: pointer; font-weight: bold; transition: transform 0.1s; }
        button:active { transform: scale(0.9); }
        .loading { font-style: italic; opacity: 0.7; }
    </style>
</head>
<body>

<div id="chat">
    <div id="messages" role="log"></div>
    <div id="inputArea">
        <input id="userInput" type="text" placeholder="Posez votre question..." autocomplete="off">
        <button id="sendBtn" onclick="handleSend()">></button>
    </div>
</div>

<script>
    // CONFIGURATION - À adapter
    const WEBHOOK_URL = "https://gnohite.app.n8n.cloud/webhook-test/aab112f4-0d24-408e-be7f-038740977e3d"; 

    const messagesContainer = document.getElementById("messages");
    const inputField = document.getElementById("userInput");

    function addMessage(text, type) {
        const div = document.createElement("div");
        div.className = `msg ${type}`;
        div.textContent = text; // Protection XSS native
        messagesContainer.appendChild(div);
        messagesContainer.scrollTop = messagesContainer.scrollHeight;
        return div;
    }

    async function handleSend() {
        const text = inputField.value.trim();
        if (!text) return;

        // 1. Affichage utilisateur
        addMessage(text, "user");
        inputField.value = "";
        
        // 2. Indicateur de chargement
        const loadingMsg = addMessage("Le bot réfléchit...", "bot loading");

        try {
            const response = await fetch(WEBHOOK_URL, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ 
                    message: text,
                    timestamp: new Date().toISOString()
                })
            });

            if (!response.ok) throw new Error(`Erreur ${response.status}`);

            const data = await response.json();
            
            // 3. Remplacement du chargement par la réponse
            loadingMsg.classList.remove("loading");
            // Ajuste 'data.output' selon la structure de ta réponse n8n
            loadingMsg.textContent = data.output || data.reply || "J'ai reçu votre message, mais n8n n'a pas renvoyé de texte.";

        } catch (error) {
            loadingMsg.remove();
            addMessage("❌ Erreur de connexion au serveur n8n.", "error");
            console.error("Détails de l'erreur:", error);
        }
    }

    inputField.addEventListener("keypress", (e) => {
        if (e.key === "Enter") handleSend();
    });
</script>

</body>
</html>
