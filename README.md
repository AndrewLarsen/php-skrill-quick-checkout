# PHP5 package for Skrill QuickCheckout interface

[![Latest Stable Version]()](https://packagist.org/packages/bupy7/yii2-page)
[![Total Downloads]()](https://packagist.org/packages/bupy7/yii2-pages)
[![License]()](https://packagist.org/packages/bupy7/yii2-pages)

UNDER DEVELOPMENT
-----------------

Simple and useful PHP5 library to make payments via Skrill QuickCheckout interface

Containing:
- Payment model with each parameter description
- Payment form generator based on payment model
- Skrill status response model with signature verifier

### Installation

Add to your composer.json

```
"require": {
    "zvook/php-skrill-quick-checkout": "*"
}
```

And run

```sh
$ composer update
```

### Usage

```php
use zvook\Skrill\Models\QuickCheckout;

$quickCheckout = new QuickCheckout([
    'pay_to_email' => 'mymoneybank@mail.com',
    'amount' => 100500,
    'currency' => 'EUR'
]);

/*
You can also use setters to bind parameters to model
If you want to see all list of parameters just open QuickCheckout file
Each class attribute has description
*/
$quickCheckout->setReturnUrl('https://my-domain.com');
$quickCheckout->setReturnUrlTarget(QuickCheckout::URL_TARGET_BLANK);
```

Build and render form

```php
use zvook\Skrill\Models\QuickCheckoutForm;

$form = new QuickCheckoutForm($quickCheckout);
$form->setFormClass('my-super-form-class');
$form->setSubmitText('Pay');
/*
By default all fields will rendered as hidden inputs
Use attribute names from QuickCheckout model as a field name
*/
$form->setVisibleFields([
    'amount' => 'label for amount field',
    'firstname' => 'label for firstname'
]);
// .....

echo $form->render();
```

In you status_url listener:

```php
use zvook\Skrill\Models\SkrillStatusResponse;
use zvook\Skrill\Components\SkrillException;

try {
    $response = new SkrillStatusResponse($_POST);
} catch (SkrillException $e) {
    # something bad in request
}

if ($response->verifySignature() && $response->isProcessed()) {
    # bingo!
}

# Or:

if ($response->isFailed()) {
    # Note that you should enable receiving failure code in Skrill account before
    # It will not provided with default settings
    $errorCode = $response->getFailedReasonCode();
}

/*
Also you can retrieve any Skrill response parameter and make extra validation you want.
To see all Skrill response parameters just view SkrillStatusResponse class attributes
For example:
*/
if ($response->getPayToEmail() !== 'mymoneybank@mail.com') {
    // hum, it's very strange ...
}
```

### Information

- Based on Skrill API version - **7.4**
- [Skrill QuickCheckout documentation](https://www.skrill.com/fileadmin/content/pdf/Skrill_Quick_Checkout_Guide.pdf)