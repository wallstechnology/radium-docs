---
title: Support
menu: Contact
form:
    name: guestbook
    fields:
        -
            name: author
            label: Name
            placeholder: 'Enter your name'
            autofocus: 'on'
            autocomplete: 'on'
            type: text
            validate:
                required: true
        -
            name: email
            label: Email
            placeholder: 'Enter your email address'
            type: email
            validate:
                required: true
        -
            name: text
            label: Message
            placeholder: 'Enter your message'
            type: textarea
            validate:
                required: true
        -
            name: date
            type: hidden
            process:
                fillWithCurrentDateTime: true
        -
            name: g-recaptcha-response
            label: Captcha
            type: captcha
            recaptcha_site_key: 6Ld7tUMUAAAAAFGdI1THiAlgYHB3BH8w_llscdQA
            recaptcha_not_validated: 'Captcha not valid!'
            validate:
                required: true
            process:
                ignore: true
    buttons:
        -
            type: submit
            value: Submit
    process:
        -
            captcha:
                recaptcha_secret: 6Ld7tUMUAAAAALgKEAEVmK7pV9raqkPUinXsldVP
        -
            email:
                subject: '[Site Guestbook] {{ form.value.name|e }}'
                body: '{% include ''forms/data.html.twig'' %}'
        -
            save:
                filename: messages.yaml
                operation: add
        -
            message: 'Thank you for writing your message!'
---

# Support questions?
Contact us.	
