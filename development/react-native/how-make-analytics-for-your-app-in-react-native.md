# How make analytics for your app in React Native

Hi, I'am Sergey and i like React Native.

Recently me needed analytics for my app in React Native and i make small script.

1. Go to [https://appmetrica.yandex.ru](https://appmetrica.yandex.ru/) , yeah this is Russian service for analytics, but he very cool.
2. Registration and create 'metrica'
3. Write this code

```text
const API_KEY = 'WRITE_THIS_YOUR_KEY'
const BOTAN_URL = 'https://api.botan.io/track';
const DEFAULT_NAME = 'Message';

function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
  //better you use really UID in your app
}

export default function sendEvent(message, name = DEFAULT_NAME) {
    return fetch(`${BOTAN_URL}/?token=${API_KEY}&name=${name}&uid=${getRandomInt(0,9999)}`, {
        headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json'
          },
          method: "POST",
          body: JSON.stringify(message)
    })
    .then(res => res.json())
    .then(data => {
      return data
    })
}

```

1. Done!

How use this function?

```text
componentDidMount() {
        getAllCategories()
        .then(data => {

            let categories = data.response.category

            sendEvent({},'loadCategories')

            this.setState({
                categories : categories,
                end_loading_categories : false
            })
        })
        .catch(() => {

            sendEvent({},'loadCategoriesError',() => {})

            this.setState({
                end_loading_categories : false
            })
        })
    }

```

Here's how it looks on the charts.

[![Charts](https://res.cloudinary.com/practicaldev/image/fetch/s--VziOO1b8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vhkqdxxbs2kn5t9zybmx.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VziOO1b8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vhkqdxxbs2kn5t9zybmx.png)

