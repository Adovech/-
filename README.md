// ==UserScript==
// @name         Add Custom Button to WhatsApp Web (Styled)
// @namespace    http://tampermonkey.net/
// @version      0.2
// @description  Додає кастомну кнопку до панелі інструментів WhatsApp Web, в стилі WhatsApp
// @author       You
// @match        https://web.whatsapp.com/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

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
            "Reporting, a delivery person seen leaving a parcel near the entrance.",
            "Reporting, a delivery person seen placing a parcel in this area.",
            "Reporting, a delivery truck seen leaving the property.",
            "Reporting, a delivery truck seen entering the property."
        ],
        jackson: [
            "Reporting, a person seen loitering in this area. Speakers were used and the person left immediately.",
            "Reporting, a suspicious person seen in this area. Speakers were used and the person left immediately.",
            "Reporting, suspicious people seen in this area. Speakers were used and they left immediately.",
            "Reporting, a suspicious person seen near the flowerbed. ",
            "Reporting, a suspicious person seen near the main entrance. ",
            "Reporting, a suspicious person seen near the black glassed door. ",
            "Reporting, a suspicious person seen near the white glassed door. ",
            "Speakers were used.",
            "Speakers were used and the person left immediately.",
            "Updating, the area is clear.",
        ],
        Calhoun: [
            "Reporting, a person seen illegally dumping trash in this area.",
            "Reporting, a person seen entering this door on the second floor.",
            "Reporting, a person seen entering the doors on the third floor.",
        ]
    };

    function isCaptionFieldOpen() {
        const captionElement = document.querySelector('.x10l6tqk.x13vifvy.xds687c.x1ey2m1c.x17qophe');
        return captionElement && captionElement.offsetParent !== null;
    }

    function insertTextIntoChat(text) {
        let inputField;
        if (isCaptionFieldOpen()) {
            inputField = document.querySelector('.x10l6tqk.x13vifvy.xds687c.x1ey2m1c.x17qophe');
        } else {
            inputField = document.querySelector('div[contenteditable="true"][data-tab="10"]');
        }

        if (inputField) {
            inputField.focus();
            document.execCommand('insertText', false, text);
        } else {
            console.error('Не знайдено поле вводу!');
        }
    }

    function addCustomButton() {
        const toolbar = document.querySelector("#app > div > div.x78zum5.xdt5ytf.x5yr21d > div > header > div > div > div > div > span > div > div:nth-child(2)");
        if (toolbar) {
            const button = document.createElement('button');
            button.classList.add(
                'x78zum5', 'x6s0dn4', 'x1afcbsf', 'x1heor9g', 'x1y1aw1k', 'x1sxyh0', 'xwib8y2', 'xurb0ha'
            );
            button.setAttribute('aria-label', 'Custom Button');
            button.setAttribute('title', 'Click Me');
            button.style.borderRadius = '50%';
            button.style.border = 'none';
            button.style.cursor = 'pointer';
            button.style.transition = 'all 0.3s ease';

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
            dropdown.style.backgroundColor = '#141b21';
            dropdown.style.border = '1px solid #ddd';
            dropdown.style.borderRadius = '8px';
            dropdown.style.padding = '15px';
            dropdown.style.zIndex = '1000';
            dropdown.style.maxHeight = '300px';
            dropdown.style.overflowY = 'auto';
            dropdown.style.boxShadow = '0 4px 8px rgba(0, 0, 0, 0.2)';
            dropdown.style.transition = 'all 0.3s ease';
            dropdown.style.minWidth = '200px';

            const select = document.createElement('select');
            select.style.marginBottom = '10px';
            select.style.padding = '8px';
            select.style.width = '100%';
            select.style.borderRadius = '6px';
            select.style.border = '1px solid #ddd';
            select.style.color = 'white';

            const mulhollandOption = document.createElement('option');
            mulhollandOption.value = 'mulholland';
            mulhollandOption.textContent = 'Мулхолланд';
            select.appendChild(mulhollandOption);

            const jacksonOption = document.createElement('option');
            jacksonOption.value = 'jackson';
            jacksonOption.textContent = 'Джексон';
            select.appendChild(jacksonOption);

            const CalhounOption = document.createElement('option');
            CalhounOption.value = 'Calhoun';
            CalhounOption.textContent = '1645';
            select.appendChild(CalhounOption);

            select.addEventListener('change', (event) => {
                updateDropdown(event.target.value);
            });

            dropdown.appendChild(select);
            document.body.appendChild(dropdown);

            function updateDropdown(option) {
                const templatesList = templates[option];
                dropdown.querySelectorAll('.template-item').forEach(item => item.remove());

                templatesList.forEach(template => {
                    const item = document.createElement('div');
                    item.classList.add('template-item');
                    item.textContent = template;
                    item.style.padding = '10px';
                    item.style.cursor = 'pointer';
                    item.style.color = 'white';
                    item.style.transition = 'background-color 0.3s ease, color 0.3s ease';
                    item.style.borderBottom = '1px solid #ddd';
                    item.style.borderRadius = '5px';

                    item.addEventListener('mouseenter', () => {
                        item.style.backgroundColor = '#1f2a30';
                        item.style.color = '#FFFFFF';
                    });

                    item.addEventListener('mouseleave', () => {
                        item.style.backgroundColor = 'transparent';
                        item.style.color = 'white';
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
                    dropdown.style.left = `${rect.left - dropdown.offsetHeight - 6}px`;
                    dropdown.style.top = `${rect.top - dropdown.offsetHeight - 2}px`;
                    dropdown.style.display = dropdown.style.display === 'none' ? 'block' : 'none';
                }
            });

            toolbar.appendChild(button);
        }
    }

    function checkAndAddButton() {
        const toolbar = document.querySelector('.x1c4vz4f.xs83m0k');
        if (toolbar) {
            addCustomButton();
        } else {
            setTimeout(checkAndAddButton, 1000);
        }
    }

    checkAndAddButton();
})();
