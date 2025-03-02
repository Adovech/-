// ==UserScript==
// @name         Add Custom Button to WhatsApp Web (Styled)
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  Додає кастомну кнопку до панелі інструментів WhatsApp Web, в стилі WhatsApp
// @author       You
// @match        https://web.whatsapp.com/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    // Список шаблонів тексту для Мулхолланд та Франклін
    const templates = {
        mulholland: [
            "Reporting, a car seen entering the property.",
            "Reporting, the car seen leaving the property.",
            "Reporting, the person seen entering the property.",
            "Reporting, the person seen leaving the property.",
            "Updating, the person seen returning to the property.",
            "Reporting, the person seen operating in this area.",
            "Reporting, a coyote seen in this area.",
            "Reporting, a delivery person seen leaving a parcel in the area.",
            "Reporting, the delivery person seen leaving a parcel near the entrance.",
            "Reporting, a delivery truck seen leaving the property.",
            "Reporting, a delivery truck seen entering the property."
        ],
        franklin: [
            "Reporting, a suspicious vehicle near the entrance.",
            "Reporting, a person loitering near the gates.",
            "Reporting, an unknown individual entering the area.",
            "Reporting, a person leaving the premises at night.",
            "Alert, strange activity observed on the premises.",
            "Suspicious person near the back alley.",
            "Caution, unknown vehicle parked in the area.",
            "Reporting, an individual acting suspiciously around vehicles.",
            "Alert, delivery vehicle parked for an extended time.",
            "Reporting, an individual near the restricted area."
        ]
    };

    // Функція для перевірки, чи відкрите поле додавання підпису
    function isCaptionFieldOpen() {
        const captionElement = document.querySelector('.x10l6tqk.x13vifvy.xds687c.x1ey2m1c.x17qophe');
        return captionElement && captionElement.offsetParent !== null; // Перевіряємо чи елемент видимий
    }

    // Функція для вставки тексту в правильне поле
    function insertTextIntoChat(text) {
        let inputField;
        if (isCaptionFieldOpen()) {
            // Якщо поле для підпису відкрите, вставляємо текст туди
            inputField = document.querySelector('.x10l6tqk.x13vifvy.xds687c.x1ey2m1c.x17qophe');
        } else {
            // Якщо не відкрите, вставляємо в основне поле для повідомлень
            inputField = document.querySelector('div[contenteditable="true"][data-tab="10"]');
        }

        if (inputField) {
            inputField.focus();
            document.execCommand('insertText', false, text);
        } else {
            console.error('Не знайдено поле вводу!');
        }
    }

    // Функція для створення і додавання кнопки
    function addCustomButton() {
        const toolbar = document.querySelector('header .x1c4vz4f.xs83m0k');

        if (toolbar) {
            const button = document.createElement('button');
            button.classList.add(
                'x78zum5', 'x6s0dn4', 'x1afcbsf', 'x1heor9g', 'x1y1aw1k', 'x1sxyh0', 'xwib8y2', 'xurb0ha'
            );
            button.setAttribute('aria-label', 'Custom Button');
            button.setAttribute('title', 'Click Me');
            button.style.marginLeft = '10px';
            button.style.backgroundColor = '#25D366'; // Оновлений колір кнопки
            button.style.borderRadius = '50%';
            button.style.padding = '8px';
            button.style.border = 'none';
            button.style.cursor = 'pointer';

            const icon = document.createElement('span');
            icon.classList.add('xdt5ytf', 'x1cy8zhl', 'x5yr21d');
            icon.innerHTML =
                `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="white">
                    <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8z"/>
                </svg>`;
            button.appendChild(icon);

            const dropdown = document.createElement('div');
            dropdown.style.position = 'absolute';
            dropdown.style.display = 'none';
            dropdown.style.backgroundColor = '#fff';
            dropdown.style.border = '1px solid #ddd';
            dropdown.style.borderRadius = '4px';
            dropdown.style.padding = '10px';
            dropdown.style.zIndex = '1000';
            dropdown.style.maxHeight = '200px';
            dropdown.style.overflowY = 'auto';
            dropdown.style.boxShadow = '0 4px 8px rgba(0, 0, 0, 0.2)';
            dropdown.style.transition = 'all 0.3s ease';

            // Додаємо вибір "Мулхолланд" або "Франклін"
            const select = document.createElement('select');
            select.style.marginBottom = '10px';
            select.style.padding = '5px';
            select.style.width = '100%';

            const mulhollandOption = document.createElement('option');
            mulhollandOption.value = 'mulholland';
            mulhollandOption.textContent = 'Мулхолланд';
            select.appendChild(mulhollandOption);

            const franklinOption = document.createElement('option');
            franklinOption.value = 'franklin';
            franklinOption.textContent = 'Франклін';
            select.appendChild(franklinOption);

            select.addEventListener('change', (event) => {
                updateDropdown(event.target.value);
            });

            dropdown.appendChild(select);
            document.body.appendChild(dropdown);

            // Оновлення шаблонів в залежності від вибору
            function updateDropdown(option) {
                const templatesList = templates[option];
                dropdown.querySelectorAll('.template-item').forEach(item => item.remove()); // Очищаємо список

                templatesList.forEach(template => {
                    const item = document.createElement('div');
                    item.classList.add('template-item');
                    item.textContent = template;
                    item.style.padding = '8px';
                    item.style.cursor = 'pointer';
                    item.style.color = 'black'; // Чорний колір тексту
                    item.style.transition = 'background-color 0.3s ease, color 0.3s ease';
                    item.style.borderBottom = '1px solid #ddd';

                    item.addEventListener('mouseenter', () => {
                        item.style.backgroundColor = '#25D366';
                        item.style.color = '#fff';
                    });

                    item.addEventListener('mouseleave', () => {
                        item.style.backgroundColor = 'transparent';
                        item.style.color = 'black'; // Повертаємо чорний колір
                    });

                    item.addEventListener('click', () => {
                        insertTextIntoChat(template);
                        dropdown.style.display = 'none';
                    });

                    dropdown.appendChild(item);
                });
            }

            button.addEventListener('click', () => {
                const captionElement = document.querySelector('.x10l6tqk.x13vifvy.xds687c.x1ey2m1c.x17qophe');
                if (captionElement) {
                    const rect = captionElement.getBoundingClientRect();
                    dropdown.style.left = `${rect.left}px`;
                    dropdown.style.top = `${rect.top - dropdown.offsetHeight - 2}px`;
                    dropdown.style.display = dropdown.style.display === 'none' ? 'block' : 'none';
                }
            });

            toolbar.appendChild(button);
        }
    }

    function checkAndAddButton() {
        const toolbar = document.querySelector('header .x1c4vz4f.xs83m0k');
        if (toolbar) {
            addCustomButton();
        } else {
            setTimeout(checkAndAddButton, 1000);
        }
    }

    checkAndAddButton();
})();
