# Shoperti PayMe

[![Build Status](https://img.shields.io/travis/dinkbit/payme.svg?style=flat-square)](https://travis-ci.org/dinkbit/payme)
[![StyleCI](https://styleci.io/repos/24345061/shield)](https://styleci.io/repos/24345061)

Based on [Active Merchant](http://github.com/Shopify/active_merchant) for Ruby.

Supported Gateways:
* Banwire Recurrent
* Conekta
* Conekta Oxxo
* Conekta Bank
* Conekta Payouts
* Stripe
* Paypal Express (soon)

## Installation

### Laravel 4.1, 4.2, 5.0, 5.1

Begin by installing this package through Composer. Edit your project's `composer.json` file to require `dinkbit/payme`.

	"require": {
		"dinkbit/payme": "dev-master"
	}

Next, update Composer from the Terminal:

    composer update

Once this operation completes, the final step is to add the service provider. Open `app/config/app.php`, and add a new item to the providers array.

    'Dinkbit\PayMe\PayMeServiceProvider'

### Add Configuration

First, you should configure the authentication providers you would like to use in your `config/services.php` file.

	return [
		'conekta' => [
			'private_key' => ':private_key',
			'public_key'  => ':public_key',
		],
		'banwire' => [
			'merchant' => ':merchant',
			'email'    => 'joe@doe.com',
		],
	];

### Examples

```php
// Inject the interface

use Dinkbit\PayMe\Contracts\Factory as PayMe;

protected $payme;

public function __construct(PayMe $payme)
{
    $this->payme = $payme;
}

public function storePost()
{
    $order = Order::create(Input::all());

    $transaction = $payme->driver('conekta')->charge($order->amount, 'tok_test');

    if (! $transaction->success()) {
    	return ':(';
    }

    return 'Hurray!';
}

```

You can override the service configuration and set specific service options.

**Interface example**

```php
$payme->driver($driver)->charge($amount, $payment, $params);

```

**Gateway example**

```php
$amount = 1000; //cents

$payme->driver('stripe')->charge($amount, 'tok_TOKEN'); // JS Token
$payme->driver('stripe')->charge($amount, 'card_TOKEN', ['customer' => 'cus_TOKEN']);
$payme->driver('stripe')->store('tok_test_visa_4242', ['name' => 'Joe Co', 'email' => 'store.guy@example.com']);
$payme->driver('stripe')->store('tok_test_visa_4242', ['customer' => 'cus_test']);
$payme->driver('stripe')->unstore('cus_test', ['card_id' => 'tok_test']);
$payme->driver('stripe')->unstore('cus_test');

$payme->driver('conekta')->charge($amount, 'tok_test_card_declined');
$payme->driver('conekta')->store('tok_test_visa_4242', ['name' => 'Joe Co', 'email' => 'store.guy@example.com']);
$payme->driver('conekta')->store('tok_test_visa_4242', ['customer' => 'cus_test']);
$payme->driver('conekta')->unstore('cus_test', ['card_id' => 'tok_test']);
$payme->driver('conekta')->unstore('cus_test');

$payme->driver('conektaoxxo')->charge($amount, 'oxxo');

$payme->driver('conektabank')->charge($amount, 'banorte');

$payme->driver('conektapayouts')->storeRecipient([
  "name"=> "Joe Co",
  "email"=> "store.guy@example.com",
  "phone"=> "55 5555 5555",
  "account_number"=> "002123456789012345", // 18 digits CLABE
  "account_holder"=> "Joe Co",
  "tax_id"=> "RFC123456QUR" // RFC
]);
$payme->driver('conektapayouts')->charge($amount, 'payee_TOKEN');
$payme->driver('conektapayouts')->unstoreRecipient('payee_TOKEN');

$payme->driver('banwirerecurrent')->charge($amount, '8305ab68d4acf7dc650364d3f31a7318', [
  'card_id' => '1407',
  'card_name' => 'Joseph Co',
  'customer' => '1'
]);

$payme->driver('bogus')->charge($amount, 'success');
$payme->driver('bogus')->charge($amount, 'fail');

```

### Todo

- [ ] Add Gateways tests
- [ ] Add more gateways
- [ ] Add Credit Card object

## License

PayMe is licensed under [The MIT License (MIT)](LICENSE).
