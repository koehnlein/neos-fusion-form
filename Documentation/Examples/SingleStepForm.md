# A single step runtime form with actions  

```
prototype(Vendor.Site:Content.SingleStepFormExample) < prototype(Neos.Fusion.Form:Runtime.RuntimeForm) {

    namespace = "single_step_form_example"

    process {

        content = afx`
            <Neos.Fusion.Form:FieldContainer field.name="firstName" label="First Name">
                <Neos.Fusion.Form:Input />
            </Neos.Fusion.Form:FieldContainer>
            <Neos.Fusion.Form:FieldContainer field.name="lastName" label="Last Name">
                <Neos.Fusion.Form:Input />
            </Neos.Fusion.Form:FieldContainer>
            <Neos.Fusion.Form:FieldContainer field.name="picture" label="Picture">
                <Neos.Fusion.Form:Upload />
            </Neos.Fusion.Form:FieldContainer>
            <Neos.Fusion.Form:FieldContainer field.name="birthDate" label="BirthDate">
                <Neos.Fusion.Form:Date />
            </Neos.Fusion.Form:FieldContainer>
            <Neos.Fusion.Form:FieldContainer field.name="sports" field.multiple label="Sports">
                <Neos.Fusion.Form:Select>
                    <Neos.Fusion.Form:Select.Option option.value="climbing" />
                    <Neos.Fusion.Form:Select.Option option.value="biking" />
                    <Neos.Fusion.Form:Select.Option option.value="hiking" />
                    <Neos.Fusion.Form:Select.Option option.value="swimming" />
                    <Neos.Fusion.Form:Select.Option option.value="running" />
                </Neos.Fusion.Form:Select>
            </Neos.Fusion.Form:FieldContainer>
        `

        schema {
            firstName = ${Form.Schema.string().isRequired()}
            lastName = ${Form.Schema.string().isRequired().validator('StringLength', {minimum: 6, maximum: 12})}
            picture = ${Form.Schema.resource().isRequired().validator('Neos\Fusion\Form\Runtime\Validation\Validator\FileTypeValidator', {allowedExtensions:['txt', 'jpg']})}
            birthDate =  ${Form.Schema.date().isRequired()}
            sports = ${Form.Schema.arrayOf( Form.Schema.string() ).validator('Count', {minimum: 1, maximum: 2})}
        }
    }

    action {
        message {
            type = 'Neos.Fusion.Form.Runtime:Message'
            options.message = afx`<h1>Thank you {data.firstName} {data.lastName}</h1>`
        }
        email {
            type = 'Neos.Fusion.Form.Runtime:Email'
            options {
                senderAddress = ${q(node).property('mailFrom')}
                recipientAddress = ${q(node).property('mailTo')}
                subject = ${q(node).property('mailSubject')}
                text = afx`Thank you {data.firstName} {data.lastName}`
                html = afx`<h1>Thank you {data.firstName} {data.lastName}</h1>`
                attachments {
                    upload = ${data.picture}
                }
            }
        }
    }
}
```
