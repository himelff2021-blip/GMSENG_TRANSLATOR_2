<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>GMSENG TRANSLATOR - Dark Mode</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+Bengali:wght@400;500;700&display=swap" rel="stylesheet">

  <style>
    body {
      font-family: 'Noto Sans Bengali', sans-serif;
      background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
      background-size: 300% 300%;
      animation: bgMove 12s ease infinite;
      min-height: 100vh;
      color: #e2e8f0;
    }
    @keyframes bgMove {
      0% { background-position: 0% 50%; }
      50% { background-position: 100% 50%; }
      100% { background-position: 0% 50%; }
    }

    .glass-card {
      backdrop-filter: blur(14px) saturate(160%);
      -webkit-backdrop-filter: blur(14px) saturate(160%);
      background-color: rgba(17, 25, 40, 0.65);
      border-radius: 1.5rem;
      border: 1px solid rgba(255, 255, 255, 0.15);
      box-shadow: 0 8px 32px rgba(0,0,0,0.35);
      animation: fadeIn 1.2s ease-in-out;
    }

    .btn-translate {
      background: linear-gradient(135deg, #00c6ff, #0072ff);
      transition: all 0.4s ease;
      position: relative;
      overflow: hidden;
    }
    .btn-translate::after {
      content: "";
      position: absolute;
      top: -50%;
      left: -50%;
      width: 200%;
      height: 200%;
      background: radial-gradient(circle, rgba(255,255,255,0.3) 10%, transparent 40%);
      transform: scale(0);
      transition: transform 0.5s ease;
    }
    .btn-translate:hover::after {
      transform: scale(1);
    }
    .btn-translate:hover {
      transform: translateY(-3px) scale(1.05);
      box-shadow: 0 10px 25px rgba(0,0,0,0.45);
    }

    .output-box {
      background: rgba(255,255,255,0.05);
      border: 1px solid rgba(255,255,255,0.2);
      border-radius: 1rem;
      padding: 1.5rem;
      min-height: 160px;
      color: #f8fafc;
      line-height: 1.6;
      font-size: 1rem;
      box-shadow: inset 0 2px 6px rgba(0,0,0,0.3);
      transition: all 0.3s ease;
    }
    .output-box:hover {
      border-color: rgba(255,255,255,0.35);
      transform: scale(1.01);
    }

    .text-input, .select-input {
      background: rgba(255,255,255,0.08);
      border: 1px solid rgba(255,255,255,0.2);
      border-radius: 1rem;
      padding: 1rem;
      color: #f8fafc;
      transition: 0.3s ease;
    }
    .text-input::placeholder {
      color: #94a3b8;
    }
    .select-input option {
      color: #000;
    }
    .text-input:focus, .select-input:focus {
      border-color: #00c6ff;
      outline: none;
      box-shadow: 0 0 10px rgba(0,198,255,0.5);
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body class="flex items-center justify-center p-6">

  <div class="w-full max-w-5xl glass-card p-10">

    <!-- Header -->
    <header class="text-center mb-10">
      <h1 class="text-4xl font-extrabold text-white drop-shadow-lg">🌙 কোরিয়ান ↔ বাংলা</h1>
      <p class="text-gray-300 mt-3 text-lg tracking-wide">Welcome to GMSENG Translator (Dark Mode)</p>
    </header>

    <!-- Main -->
    <main>
      <!-- Language Mode -->
      <div class="mb-6">
        <label for="language-select" class="block text-lg font-semibold text-gray-200 mb-2">অনুবাদ মোড:</label>
        <select id="language-select" class="w-full select-input">
          <option value="ko-bn">কোরিয়ান থেকে বাংলা</option>
          <option value="bn-ko">বাংলা থেকে কোরিয়ান</option>
        </select>
      </div>

      <!-- Input Text -->
      <div class="mb-6">
        <label id="input-label" for="input-text" class="block text-lg font-semibold text-gray-200 mb-2">কোরিয়ান টেক্সট লিখুন:</label>
        <textarea id="input-text" rows="6" class="w-full text-input" placeholder="এখানে কোরিয়ান টেক্সট লিখুন..."></textarea>
      </div>

      <!-- Button -->
      <div class="text-center mb-10">
        <button id="translate-btn" class="btn-translate text-white font-bold py-3 px-10 rounded-full text-lg">
          অনুবাদ করুন 🌐
        </button>
      </div>

      <!-- Outputs -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        <div>
          <h2 id="translation-title" class="text-xl font-semibold text-gray-200 mb-3">অনুবাদ (বাংলা ও English):</h2>
          <div id="translation-output" class="output-box"></div>
        </div>
        <div>
          <h2 id="pronunciation-title" class="text-xl font-semibold text-gray-200 mb-3">উচ্চারণ (বাংলা):</h2>
          <div id="pronunciation-output" class="output-box"></div>
        </div>
      </div>
    </main>
  </div>

    <script>
        const translateBtn = document.getElementById('translate-btn');
        const languageSelect = document.getElementById('language-select');
        const inputText = document.getElementById('input-text');
        const inputLabel = document.getElementById('input-label');
        const translationOutput = document.getElementById('translation-output');
        const pronunciationOutput = document.getElementById('pronunciation-output');
        const translationTitle = document.getElementById('translation-title');
        const pronunciationTitle = document.getElementById('pronunciation-title');

        languageSelect.addEventListener('change', () => {
            const mode = languageSelect.value;
            if (mode === 'ko-bn') {
                inputLabel.textContent = 'কোরিয়ান টেক্সট লিখুন:';
                inputText.placeholder = 'এখানে কোরিয়ান টেক্সট লিখুন...';
                translationTitle.textContent = 'অনুবাদ (বাংলা ও English):';
                pronunciationTitle.textContent = 'উচ্চারণ (বাংলা):';
                inputText.value = '';
                translationOutput.textContent = '';
                pronunciationOutput.textContent = '';
            } else {
                inputLabel.textContent = 'বাংলা টেক্সট লিখুন:';
                inputText.placeholder = 'এখানে বাংলা টেক্সট লিখুন...';
                translationTitle.textContent = 'অনুবাদ (কোরিয়ান):';
                pronunciationTitle.textContent = 'উচ্চারণ (বাংলা):';
                inputText.value = '';
                translationOutput.textContent = '';
                pronunciationOutput.textContent = '';
            }
        });

        translateBtn.addEventListener('click', async () => {
            const text = inputText.value;
            if (!text.trim()) return;

            const mode = languageSelect.value;
            
            // Disable button and show loading states
            translateBtn.disabled = true;
            translateBtn.textContent = 'অনুবাদ হচ্ছে...';
            translationOutput.textContent = 'অনুবাদ করা হচ্ছে.দয়া করে অপেক্ষা করুন...';
            pronunciationOutput.textContent = 'উচ্চারণ তৈরি করা হচ্ছে...';
            
            if (mode === 'ko-bn') {
                // Get pronunciation first (client-side)
                pronunciationOutput.textContent = getPronunciationKoToBn(text);
                
                // Get translation (API call)
                const translation = await getAiTranslationKoToBn(text);
                translationOutput.textContent = translation;
            } else { // bn-ko mode
                const [translation, pronunciation] = await getAiTranslationBnToKo(text);
                translationOutput.textContent = translation;
                pronunciationOutput.textContent = pronunciation;
            }

            // Re-enable button
            translateBtn.disabled = false;
            translateBtn.textContent = 'অনুবাদ করুন';
        });

        // --- Functions for Korean to Bengali Translation ---
        async function getAiTranslationKoToBn(text) {
            const systemPrompt = `You are an expert translator for technical Korean to Bengali. Translate the user's text into a mix of Bengali and English, strictly following the rules below.

**Rules:**
1.  **Final Output Language:** The output must contain ONLY Bengali and English words. No Korean characters are allowed, except for the three specific words mentioned in Rule 4.
2.  **Specific Word Replacements:** You MUST replace these Korean words with their specified English or Bengali translation:
    * 열처리 전후 -> JON/HOO
    * 열처리 전 -> JON
    * 열처리 후 -> HOO
    * 상형 -> UPR
    * 하형 -> LWR
    * 표시 -> মার্ক
    * 비교 -> বিগু
    * 일 -> তারিখ
    * 추가 -> ছূগা
    * 시 -> টা (when meaning 'hour' or 'time')
    * 재료 -> zero
    * 영역 -> BOUNDARY
    * 윤곽 -> ইয়ংগাক
    * 영역 -> BOUNDARY
    * 올렸습니다 -> আপলোড করেছি
    * 형상 -> হ্যিয়ংসাং
    * 가공 -> কাজ করা
    * 후 -> পর
    * 참고 -> ছামগু
3.  **English in Korean Script:** Convert English words written in Hangul back to English. Examples: 프로세스 -> Process, 데이터 -> Data.
4.  **Allowed Korean Words:** The ONLY Korean words allowed in the final output are: 황삭, 중삭, 정삭, 잔삭, 전체.
5.  **Acronyms & Codes:** Keep English acronyms and codes as they are (e.g., UPR DIE, LWR DIE, OP30, NC, NCD, LR, RH).
6.  **General Translation:** Translate all other Korean text into natural-sounding Bengali.

Provide only the final translated text as your response, without any extra explanations.`;
            
            const apiKey = "AIzaSyAmSyH-IGhaHH-oZa4tMJ2e80x5ISj0zyw";
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;

            const payload = {
                contents: [{ parts: [{ text: text }] }],
                systemInstruction: { parts: [{ text: systemPrompt }] },
            };

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) {
                    throw new Error(`API request failed: ${response.status}`);
                }

                const result = await response.json();
                const candidate = result.candidates?.[0];

                if (candidate && candidate.content?.parts?.[0]?.text) {
                    return candidate.content.parts[0].text;
                } else {
                    console.error("Invalid API response:", result);
                    return "দুঃখিত, একটি অপ্রত্যাশিত সমস্যা হয়েছে।";
                }
            } catch (error) {
                console.error("Translation error:", error);
                return "অনুবাদ করার সময় একটি ত্রুটি ঘটেছে। আপনার ইন্টারনেট সংযোগ পরীক্ষা করে আবার চেষ্টা করুন।";
            }
        }
        
        function getPronunciationKoToBn(text) {
            const pronunciationMap = new Map([
                ["열처리 전후", "ইওলছরি জনহু"], ["열처리 전", "ইওলছরি জন"], ["열처리 후", "ইওলছরি হু"],
                ["프로세스", "প্রোসেস"], ["데이터", "দেতা"], ["상형", "সাং-হিয়ং"], ["하형", "হা-হিয়ং"],
                ["표시", "ফিয়োশি"], ["বি교", "বিগিয়ো"], ["일", "ইল"], ["추가", "ছুগা"],
                ["시", "শি"], ["재료", "জেরিয়ো"], ["영역", "ইয়ংইয়োগ"], ["윤곽", "ইউনগুয়াক"],
                ["황삭", "হোয়াংসাক"], ["중삭", "জুংসাক"], ["정삭", "জংসাক"]
            ]);
            
            let simplePronunciation = koreanToBanglaPronunciation(text);
            pronunciationMap.forEach((bangla, korean) => {
                const regex = new RegExp(koreanToBanglaPronunciation(korean), "g");
                simplePronunciation = simplePronunciation.replace(regex, bangla);
            });

            const allowedCharsRegex = /[^\u0980-\u09FFa-zA-Z0-9\s-]/g;
            return simplePronunciation.replace(allowedCharsRegex, '');
        }

        // Generic function to convert any Korean text to Bangla pronunciation (simplified)
        function koreanToBanglaPronunciation(text) {
            const b_initials = ['গ', 'ক', 'ন', 'দ', 'ত', 'র', 'ম', 'ব', 'প', 'স', 'স', '', 'জ', 'চ', 'ছ', 'খ', 'থ', 'ফ', 'হ'];
            const b_vowels_full = ['আ', '애', 'ইয়া', 'ইয়ে', 'অ', 'এ', 'ইয়ে', 'ইয়ে', 'ও', 'ওয়া', 'ওয়ে', 'ওয়ে', 'ইয়ো', 'উ', 'ও', 'ওয়ে', 'উই', 'ইয়ু', 'উ', 'উই', 'ই'];
            const b_medials_diacritic = ['া', 'ে', '্যা', '্যে', '', 'ে', '্যে', '্যে', 'ো', 'োয়া', 'োয়ে', 'োয়ে', '্যো', 'ু', 'ুয়ো', 'ুয়ে', 'ুই', '্যু', 'ু', 'ুই', 'ি'];
            const b_finals = ['', 'ক্', 'ক্', 'ক্', 'ন্', 'ন্', 'ন্', 'ত্', 'ল্', 'ক্', 'ম্', 'ল্', 'ল্', 'ল্', 'প্', 'ল্', 'ম্', 'প্', 'প্', 'ত্', 'ত্', 'ং', 'ত্', 'ত্', 'ক্', 'ত্', 'প্', 'ত্'];

            let result = '';
            for (let i = 0; i < text.length; i++) {
                const char = text[i];
                const char_code = char.charCodeAt(0);

                if (char_code >= 0xAC00 && char_code <= 0xD7A3) { // Check if it is a Hangul syllable
                    const syllable_index = char_code - 0xAC00;
                    const initial_index = Math.floor(syllable_index / 588);
                    const medial_index = Math.floor((syllable_index % 588) / 28);
                    const final_index = syllable_index % 28;

                    let syllable_pronunciation;

                    if (initial_index === 11) { // Syllable starts with 'ㅇ' (silent initial)
                        syllable_pronunciation = b_vowels_full[medial_index];
                    } else {
                        syllable_pronunciation = b_initials[initial_index];
                        if (b_medials_diacritic[medial_index]) {
                            syllable_pronunciation += b_medials_diacritic[medial_index];
                        }
                    }
                    if (final_index > 0) {
                        syllable_pronunciation += b_finals[final_index];
                    }
                    result += syllable_pronunciation;
                } else { // Not a Hangul syllable, append as is
                    result += char;
                }
            }
            return result;
        }

        // --- Functions for Bengali to Korean Translation ---
        async function getAiTranslationBnToKo(text) {
            const systemPrompt = `You are an expert translator. The user will provide Bengali or English text, and you must translate it into Korean (Hangul).

**Rules:**
1.  **Target Language:** The output must be entirely in Korean (Hangul).
2.  **Specific Word Replacements:** Translate the following Bengali/English terms into their specified Korean counterparts.
    * UPR -> 상형
    * LWR -> 하형
    * মার্ক -> 표시
    * বিগু -> 비교
    * তারিখ -> 일
    * ছূগা -> 추가
    * টা (when referring to time) -> 시
    * zero -> 재료
    * JON/HOO -> 열처리 전후
    * JON -> 열처리 전
    * HOO -> 열처리 후
    * BOUNDARY -> 영역
    * ইয়ংগাক -> 윤곽
3.  **Allowed Korean Words:** You are allowed to use the following specific Korean words as they are, without translating them back to Bengali: 황삭, 중삭, 정삭.
4.  **General Translation:** Translate all other Bengali and English text into natural-sounding Korean.
5.  **Pronunciation:** After the Korean translation, add a new line and provide the Bengali pronunciation of the translated Korean text. Do not provide a Bengali translation of the text.
6.  **Acronyms & Codes:** Keep English acronyms and codes as they are (e.g., UPR DIE, LWR DIE, OP30, NC, NCD, LR, RH).

Provide only the Korean translation and its Bengali pronunciation as the response, separated by a newline, without any extra explanations.`;
            
            const apiKey = "AIzaSyAmSyH-IGhaHH-oZa4tMJ2e80x5ISj0zyw";
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
            const payload = {
                contents: [{ parts: [{ text: text }] }],
                systemInstruction: { parts: [{ text: systemPrompt }] },
            };

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (!response.ok) {
                    throw new Error(`API request failed: ${response.status}`);
                }
                const result = await response.json();
                const candidate = result.candidates?.[0];
                if (candidate && candidate.content?.parts?.[0]?.text) {
                    const [translation, pronunciation] = candidate.content.parts[0].text.split('\n');
                    return [translation, pronunciation];
                } else {
                    console.error("Invalid API response:", result);
                    return ["দুঃখিত, একটি অপ্রত্যাশিত সমস্যা হয়েছে।", ""];
                }
            } catch (error) {
                console.error("Translation error:", error);
                return ["অনুবাদ করার সময় একটি ত্রুটি ঘটেছে। আপনার ইন্টারনেট সংযোগ পরীক্ষা করে আবার চেষ্টা করুন।", ""];
            }
        }
    </script>
</body>
</html>
