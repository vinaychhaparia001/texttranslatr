# texttranslatr

npx create-react-app text-translator
cd text-translator

npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
@tailwind base;
@tailwind components;
@tailwind utilities;
import React, { useState } from 'react';

const languages = [
  { code: 'es', name: 'Spanish' },
  { code: 'fr', name: 'French' },
  { code: 'de', name: 'German' },
  { code: 'it', name: 'Italian' },
  { code: 'pt', name: 'Portuguese' },
  { code: 'ru', name: 'Russian' },
  { code: 'ja', name: 'Japanese' },
  { code: 'ko', name: 'Korean' },
  { code: 'zh', name: 'Chinese (Simplified)' },
  { code: 'ar', name: 'Arabic' },
];

function App() {
  const [inputText, setInputText] = useState('');
  const [selectedLang, setSelectedLang] = useState('es');
  const [translatedText, setTranslatedText] = useState('');
  const [isLoading, setIsLoading] = useState(false);

  const translateText = async () => {
    if (!inputText.trim()) return;

    setIsLoading(true);
    const encodedParams = new URLSearchParams();
    encodedParams.set('q', inputText);
    encodedParams.set('target', selectedLang);
    encodedParams.set('source', 'en');

    const options = {
      method: 'POST',
      headers: {
        'content-type': 'application/x-www-form-urlencoded',
        'Accept-Encoding': 'application/gzip',
        'X-RapidAPI-Key': process.env.REACT_APP_RAPIDAPI_KEY, // Store your API key in .env
        'X-RapidAPI-Host': 'google-translate1.p.rapidapi.com'
      },
      body: encodedParams
    };

    try {
      const response = await fetch('https://google-translate1.p.rapidapi.com/language/translate/v2', options);
      const data = await response.json();
      setTranslatedText(data.data.translations[0].translatedText);
    } catch (error) {
      console.error('Translation error:', error);
      setTranslatedText('Error: Unable to translate. Please try again.');
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 flex items-center justify-center p-4">
      <div className="bg-white rounded-lg shadow-lg p-8 w-full max-w-2xl">
        <h1 className="text-3xl font-bold text-center text-gray-800 mb-8">Text Translator</h1>
        
        <div className="mb-6">
          <label htmlFor="inputText" className="block text-sm font-medium text-gray-700 mb-2">
            English Text
          </label>
          <textarea
            id="inputText"
            rows="4"
            className="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500"
            placeholder="Enter text to translate..."
            value={inputText}
            onChange={(e) => setInputText(e.target.value)}
          />
        </div>
        
        <div className="mb-6">
          <label htmlFor="languageSelect" className="block text-sm font-medium text-gray-700 mb-2">
            Target Language
          </label>
          <select
            id="languageSelect"
            className="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500"
            value={selectedLang}
            onChange={(e) => setSelectedLang(e.target.value)}
          >
            {languages.map((lang) => (
              <option key={lang.code} value={lang.code}>
                {lang.name}
              </option>
            ))}
          </select>
        </div>
        
        <button
          onClick={translateText}
          disabled={isLoading || !inputText.trim()}
          className="w-full bg-indigo-600 text-white py-2 px-4 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 disabled:opacity-50 disabled:cursor-not-allowed mb-6"
        >
          {isLoading ? 'Translating...' : 'Translate'}
        </button>
        
        {translatedText && (
          <div className="bg-gray-50 rounded-md p-4">
            <h3 className="text-lg font-medium text-gray-900 mb-2">Translated Text:</h3>
            <p className="text-gray-700">{translatedText}</p>
          </div>
        )}
      </div>
    </div>
  );
}

export default App;
REACT_APP_RAPIDAPI_KEY=your_rapidapi_key_here
