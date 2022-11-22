## Messaging System

The messagin system or the event system is the communication between the Vue Framework of the Aixelink Webapp and the iFrame of the
Forms which are triggered or opened. This is the core architecture of Aixelink Connect in which the forms work.

## Methodology

Whenever the iFrame tries to open up a new form it should send the necessary data (i.e: wfcode, eventid) to the Framework. And the 
LaunchICUFormEvent is dispatched.

We will use **icusearchform** as an example to understand it.

   >**Note:** <i>formdata.js</i> is the iFrame part of the app and <i>index.vue</i> is the framework (Vue application which this app is built on)

   > src of <i>formdata.js</i> : `static\Operational\ICUSearchForm\local\formdata.js`
    
> src of <i>index.vue</i> : `src\views\home\index.vue`

When the button will be clicked from the **formdata.js** it will dispatch an event which is called ``LaunchICUFormEvent``. And the same event is listening
on the ``mounted()`` of the **index.vue**

formdata.js :

```js
async onClick(e) {
    try {
        console.log(e.row.data)
        savePatient(e.row.data)
        await Store.activatePatient(e.row.data.EventId)
        let event = new CustomEvent('LaunchICUFormEvent', {
            detail: {
                wfcode: 'DashboardV1',
                eventId: e.row.data.EventId,
                admdate: e.row.data.AdmissionDate,
                dischargedate: e.row.data.DischargeDate,
            },
        })
        window.parent.document.dispatchEvent(event)
    } catch (e) {
        console.error(e)
    }
},
```

index.vue :

```js
mounted() {
    let iframe = document.getElementById('homeview-iframe') as HTMLIFrameElement
    let iFrameWindow = iframe.contentWindow as IFormWindow
    iFrameWindow.store = store
    window.document.addEventListener('LaunchICUFormEvent', (e: any) => {
        this.launchForm({ ...e.detail })
    })
},
```

In the framework where ``LaunchICUFormEvent`` event is listened, it is calling a function ``this.launchForm({ ...e.detail })``. The function is

```js
async launchForm(params: { wfcode: WorkflowCodes; eventId: string; [key: string]: any }): Promise<void> {
    let wfcode = params.wfcode
    let eventId = params.eventId
    let admdate = params.admdate
    let dischargedate = params.dischargedate
    if (eventId === null || eventId === undefined) {
        throw new Error('eventid is required')
    }
    let url = store.getters.forms.find((form: IForm) => form.code === wfcode).link

    //
    url = url.replace('[patient|eventid]', eventId)
    url = url.replace('[patient|admdate]', btoa(admdate))
    url = url.replace('[patient|dischargedate]', btoa(dischargedate))
    try {
        let event = new CustomEvent('UrlGetEvent', {
            detail: { url: url, title: getWfTitle(wfcode), titleTemplate: getWfTitleTemplate(wfcode) },
        })
        let iframe = document.getElementById('homeview-iframe') as HTMLIFrameElement
        // let iFrameWindow = iframe.contentWindow as IFormWindow
        // iFrameWindow.store = store
        iframe.contentWindow!.dispatchEvent(event)
    } catch (e) {
        console.error(e)
    }
},
```

The function is getting all the necessary data and parameters necessary for the event call ``LaunchICUFormEvent``. It is getting the wfcode, eventId, admdate, dischargedate from the parameters and generates url from the database that is running on the system.

With having all the necessary variables it is dispatching a new ``CustomEvent`` on the iFrame window called  ``iframe.contentWindow!``


```js
try {
    let event = new CustomEvent('UrlGetEvent', {
        detail: { url: url, title: getWfTitle(wfcode), titleTemplate: getWfTitleTemplate(wfcode) },
    })
    let iframe = document.getElementById('homeview-iframe') as HTMLIFrameElement
    // let iFrameWindow = iframe.contentWindow as IFormWindow
    // iFrameWindow.store = store
    iframe.contentWindow!.dispatchEvent(event)
} catch (e) {
    console.error(e)
}
```

After the ``UrlGetEvent`` is dipatched, the ``LaunchICUFormEvent`` gets all the necessary data and the Form is launched.

