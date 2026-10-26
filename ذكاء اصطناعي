-- KEON Hub AI Bot Module (v1.0)
return function(data)
    local AI_API_KEY = data.AI_API_KEY
    local customRequest = data.customRequest
    local safeDecode = data.safeDecode
    local FIREBASE_CHAT_URL = data.FIREBASE_CHAT_URL
    local HttpService = game:GetService("HttpService")

    local function askAIBot(promptText)
        local success, res = pcall(function()
            return customRequest({
                Url = "https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=" .. AI_API_KEY,
                Method = "POST",
                Headers = {["Content-Type"] = "application/json"},
                Body = HttpService:JSONEncode({
                    contents = {
                        {
                            parts = {
                                { text = "أنت ذكاء اصطناعي مساعد لطيف جداً داخل سكربت روبلوكس اسمه KEON Hub، ومطورك الأساسي هو كيون. رد على هذا السؤال أو الحديث باللهجة العراقية الودودة وبأسرع وقت واختصار: " .. promptText }
                            }
                        }
                    }
                })
            })
        end)
        if success and res and res.StatusCode == 200 then
            local resData = safeDecode(res.Body)
            if resData and resData.candidates and resData.candidates[1] and resData.candidates[1].content and resData.candidates[1].content.parts[1] then
                return resData.candidates[1].content.parts[1].text
            end
        end
        return "هلا بيك! عيون وخدمة لك، كيون مجهز كلشي بأعلى احترافية ولعيونك ⚡"
    end

    local processedAiMessages = {}
    task.spawn(function()
        while true do
            pcall(function()
                local res = customRequest({Url = FIREBASE_CHAT_URL, Method = "GET"})
                if res and res.StatusCode == 200 then
                    local chatData = safeDecode(res.Body)
                    if chatData then
                        for k, m in pairs(chatData) do
                            if type(m) == "table" and m.text and not processedAiMessages[k] then
                                if not m.isSticker and m.userId ~= 1 then
                                    local lowerText = string.lower(tostring(m.text))
                                    if string.find(lowerText, "كيون") or string.find(lowerText, "keon") or string.find(lowerText, "bot") or string.find(lowerText, "سكربت") then
                                        processedAiMessages[k] = true
                                        task.spawn(function()
                                            task.wait(1.5)
                                            local aiReply = askAIBot(m.text)
                                            pcall(function()
                                                customRequest({
                                                    Url = FIREBASE_CHAT_URL,
                                                    Method = "POST",
                                                    Headers = {["Content-Type"] = "application/json"},
                                                    Body = HttpService:JSONEncode({
                                                        sender = "🤖 ذكاء KEON الاصطناعي",
                                                        userId = 1,
                                                        text = aiReply,
                                                        isSticker = false,
                                                        time = os.time()
                                                    })
                                                })
                                            end)
                                        end)
                                    end
                                end
                            end
                        end
                    end
                end
            end)
            task.wait(2.5)
        end
    end)
end
