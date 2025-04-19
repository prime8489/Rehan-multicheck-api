const express = require("express");
const axios = require("axios");

const app = express();
app.use(express.json());

// ✅ Home Route
app.get("/", (req, res) => {
    res.send("API is running successfully!");
});

// ✅ Membership Check Route (multi-bot-token support)
app.get("/check-membership", async (req, res) => {
    const { user_id, chat_id, token } = req.query;

    if (!user_id || !chat_id || !token) {
        return res.json({
            status: "error",
            message: "User ID, Chat ID(s), and Token are required"
        });
    }

    const channels = JSON.parse(decodeURIComponent(chat_id));
    let notJoinedChannels = [];

    try {
        for (let channel of channels) {
            let response = await axios.get(`https://api.telegram.org/bot${token}/getChatMember`, {
                params: { chat_id: channel, user_id: user_id }
            });

            let status = response.data.result.status;
            if (status === "left" || status === "kicked") {
                notJoinedChannels.push(channel);
            }
        }

        if (notJoinedChannels.length === 0) {
            return res.json({ status: "true", is_joined: true });
        } else {
            return res.json({
                status: "false",
                is_joined: false,
                not_joined_channels: notJoinedChannels
            });
        }
    } catch (error) {
        console.error("Error checking channels:", error.response ? error.response.data : error.message);
        return res.json({ status: "error", message: "Failed to check channels" });
    }
});

// ✅ Start Server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
