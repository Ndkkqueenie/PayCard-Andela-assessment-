# PayCard-Andela-assessment-
PayCard Web app 
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Mini App</title>
    <style>
      body {
        margin: 0;
        padding: 1em;
        background-color: #fff;
      }
      
      [data-cart-info],
      [data-credit-card] {
        transform: scale(0.78);
    	margin-left: -3.4em;
      }
      
      [data-cart-info] span {
        display: inline-block;
        vertical-align: middle;
        
      }
      
      span.material-icons {
        font-size: 150px;
      } 
      
      [data-credit-card] {
        width: 435px;
        min-height: 240px;
        border-radius: 10px;
        background-color: #5d6874;
      }
​
      [data-cc-info] input:focus,
      [data-cc-digits] input:focus {
        outline: none;
      }
      
      [data-cc-digits] {
        margin-top: 2em;
      }
      
      [data-cc-digits] input {
        color: #fff;
        font-size: 2em;
        line-height: 2em;
        border: none;
        background: transparent;
        margin-right: 0.5em;
      }
      
      [data-cc-info] {
        margin-top: 1em;
      }
      
      [data-cc-info] input {
        color: #fff;
        font-size: 1.2em;
        border: none;
        background: transparent;
      }
      
      [data-cc-info] input:nth-child(2) {
        padding-right: 10px;
        float: right;
      }
​
      .mdc-card__primary-action,
      .mdc-card__primary-action:hover {
        cursor: auto;
        padding: 20px;
        min-height: inherit;
      }
      
      [data-credit-card] [data-card-type] {
        transition: width 1.5s;
        margin-left: calc(100% - 130px);
      }
      
      [data-card-type] {
        display: block;
        width: 120px;
        height: 60px;
      }
​
      [data-credit-card].is-visa {
        background: linear-gradient(135deg, #622774 0%, #c53364 100%);
      }
​
      [data-credit-card].is-mastercard {
        background: linear-gradient(135deg, #65799b 0%, #5e2563 100%);
      }
​
      .is-visa [data-card-type],
      .is-mastercard [data-card-type] {
        width: auto;
      }
​
      input.is-invalid,
      .is-invalid input {
        text-decoration: line-through;
      }
​
      ::placeholder {
        color: #fff;
      }
      
      [data-pay-btn] {
        position: fixed;
        width: 90%;
        border: solid 1px;
        bottom: 20px;
      }
    </style>
  </head>
  <body>
    <div data-cart-info>
      <h class="mdc-typography--headline4">
        <span class="material-icons">shopping_cart</span>
        <span data-bill></span>
      </h>
    </div>
    <div data-credit-card class="mdc-card mdc-card--outlined">
      <div class="mdc-card__primary-action">
        <img data-card-type src="https://placehold.it/120x60.png?text=Card">
        <div data-cc-digits>
          <input type="text" size="4" placeholder="----">
          <input type="text" size="4" placeholder="----">
          <input type="text" size="4" placeholder="----">
          <input type="text" size="4" placeholder="----">
        </div>
        <div data-cc-info>
          <input type="text" size="20" placeholder="Name Surname">
          <input type="text" size="6" placeholder="MM/YY">
        </div>
      </div>
    </div>
    <button class="mdc-button" data-pay-btn>Pay & Checkout Now</button>
    <script>
      const supportedCards = {
        visa, mastercard
      };
      
      const countries = [
        {
          code: "US",
          currency: "USD",
          country: 'United States'
        },
        {
          code: "NG",
          currency: "NGN",
          country: 'Nigeria'
        },
        {
          code: 'KE',
          currency: 'KES',
          country: 'Kenya'
        },
        {
          code: 'UG',
          currency: 'UGX',
          country: 'Uganda'
        },
        {
          code: 'RW',
          currency: 'RWF',
          country: 'Rwanda'
        },
        {
          code: 'TZ',
          currency: 'TZS',
          country: 'Tanzania'
        },
        {
          code: 'ZA',
          currency: 'ZAR',
          country: 'South Africa'
        },
        {
          code: 'CM',
          currency: 'XAF',
          country: 'Cameroon'
        },
        {
          code: 'GH',
          currency: 'GHS',
          country: 'Ghana'
        }
      ];
      
      const appState = {};
      
      const formatAsMoney = (amount, buyerCountry) => {
      let code = 'US';
      let currency = 'USD';
​
      countries.forEach(countr => {
        if (countr.country === buyerCountry) {
          code = countr.code;
          currency = countr.currency;
        }
      });
      const bill = amount.toLocaleString(`en-${code}`, {
        style: 'currency',
        currency: currency
      });
​
      return bill
    };
      
      const flagIfInvalid = (field, isValid) => {
        isValid?field.classList.remove('is-invalid'):field.classList.add('is-invalid');
      }
      
      const expiryDateFormatIsValid = (target) => {
       	const pattern = /^(0[1-9])|(1[0-2])\/[0-9]{2}$/;
        return pattern.test(target.value);
      }
      
      const detectCardType = ({target}) => {
        const el = document.querySelector('[data-credit-card]'),
              img = document.querySelector('[data-card-type]');
        
        if ((/^4[0-9]{3}$/i).test(target.value)) {
          el.classList.add('is-visa');
          el.classList.remove('is-mastercard');
          img.src = supportedCards.visa;
          return 'is-visa';
        }
        else if ((/^5[0-9]{3}$/i).test(target.value)) {
          el.classList.add('is-mastercard');
          el.classList.remove('is-visa');
          img.src = supportedCards.mastercard;
          return 'is-mastercard';
        }
      };
      
      const validateCardExpiryDate = ({ target }) => {
        const d = target.value.split('/');
​
        const formattedCardDate = new Date(`${d[0]}/01/${d[1]}`);
        const isFutureDate = () => (formattedCardDate > new Date());
        const check = expiryDateFormatIsValid(target) && isFutureDate();
        if (check) {
          flagIfInvalid(target, true);
          return true
        } else {
          flagIfInvalid(target, false);
          return false;
        }
      };
      
      const validateCardHolderName = ({target}) => {
        const isValid = (/^[a-z]{3,} [a-z]{3,}$/i).test(target.value);
        flagIfInvalid(target, isValid);
        if (isValid) {
          return true;
        }
        else {
          return false;
        }
       
      };
      
      const validateWithLuhn = (digits) => {
        if (digits.length != 16) return false;
        
        let sum = 0, digit = '';
        
        for (let i = digits.length - 1; i >= 0; i--) {
          digit = digits[i];
          if (i % 2 === 0) {
            digit *= 2;
            if (digit > 9) {
              digit -= 9;
            }
          }
          sum += digit; 
        }
        return sum % 10 === 0;
      }
      
      const validateCardNumber = () => {
        let digits = []
        
        const inputDiv = document.querySelector('[data-cc-digits]'),
              inputs = document.querySelectorAll('[data-cc-digits] input');
        inputs.forEach(input => digits.push(input.value));
        digits = digits.join('').split('');
        digits.forEach((o, i, a) => {
          a[i] = Number(o);
        });
          
        if (validateWithLuhn(digits)){
          inputDiv.classList.remove('is-invalid');
          return true;
        }
        else {
          inputDiv.classList.add('is-invalid');
          return false;
        }
        
      };
      
      const uiCanInteract = () => {
        document.querySelector('[data-cc-digits] input').addEventListener('blur', detectCardType);
        document.querySelector('[data-cc-info] input').addEventListener('blur', validateCardHolderName);
        document.querySelectorAll('[data-cc-info] input')[1].addEventListener('blur', validateCardExpiryDate);
        document.querySelector('[data-pay-btn]').addEventListener('click', validateCardNumber);
        document.querySelector('[data-cc-digits] input').focus();
      };
      
      const displayCartTotal = ({ results }) => {
        const [data] = results;
      	const { itemsInCart, buyerCountry } = data;
      	appState.items = itemsInCart;
      	appState.country = buyerCountry;
      	appState.bill = itemsInCart.reduce((acc, item) => acc + (item.price * item.qty), 0);
      	appState.billFormatted = formatAsMoney(appState.bill, appState.country);
      	const displayBill = document.querySelector('span[data-bill]');
      	displayBill.textContent = appState.billFormatted;
      	uiCanInteract();
      };
      
      const fetchBill = () => {
        const api = 'https://randomapi.com/api/006b08a801d82d0c9824dcfdfdfa3b3c';
        fetch(api)
        	.then(response => response.json())
        	.then(data => displayCartTotal(data))
        	.catch(error => console.log(error));
      };
      
      const startApp = () => {
        fetchBill();
      };
​
      startApp();
    </script>
  </body>
</html>
