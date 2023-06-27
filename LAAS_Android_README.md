#Introduction
How to setup the ANDROID LAAS SDK 

## Installation
``` groovy
Add mavenCentral() in project build.gradle
```

``` groovy
Add implementation 'co.leatherback:leatherback-checkout-android:1.0.17-alpha02' 
```
### You can find the latest version in the url below:
[https://mvnrepository.com/artifact/co.leatherback/leatherback-checkout-android](https://mvnrepository.com/artifact/co.leatherback/leatherback-checkout-android)

### 1. Create an activity result launcher to start activity for a result.

Here Is a snippet:

Kotlin Code:

``` kotlin
private val startActivityResult: ActivityResultLauncher<Intent> = registerForActivityResult(
        ActivityResultContracts.StartActivityForResult()
    ) {
        val data = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
            it.data?.getParcelableExtra(
                LeatherBackConstants.IntentParams.LEATHERBACK_RESULT,
                LeatherBackTransactionResponse::class.java
            )
        } else {
            it.data?.getParcelableExtra(
                LeatherBackConstants.IntentParams.LEATHERBACK_RESULT
            )
        }
        if (it.resultCode == RESULT_OK && data != null) {

            when (data.status) {
                LeatherBackConstants.LeatherBackTransactionStatus.SUCCESS -> {

                }

                LeatherBackConstants.LeatherBackTransactionStatus.FAILED -> {

                }
                LeatherBackConstants.LeatherBackTransactionStatus.ABORTED -> {

                }
            }
        }
    } 
```

Java Code: 

``` java
 private ActivityResultLauncher<Intent> startActivityResult = registerForActivityResult(
        new ActivityResultContracts.StartActivityForResult(),
        result -> {
            Intent intent = result.getData();
            if (result.getResultCode() == RESULT_OK && intent != null) {
                LeatherBackTransactionResponse data;
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
                    data = intent.getParcelableExtra(
                            LeatherBackConstants.IntentParams.LEATHERBACK_RESULT,
                            LeatherBackTransactionResponse.class
                    );
                } else {
                    data = intent.getParcelableExtra(
                            LeatherBackConstants.IntentParams.LEATHERBACK_RESULT
                    );
                }
                if (data != null) {
                    switch (data.getStatus()) {
                        case SUCCESS:
                            
                            break;
                        case FAILED:
                            
                            break;
                        case ABORTED:
                            
                            break;
                    }
                }
}
```

### 2. Create Checkout Builder Patter

Here is a snippet

Kotlin Code:

``` kotlin
val leatherbackCheckout = LeatherbackCheckoutParcel
                .Builder()
                .setAmount(20.toDouble())
                .setApiKey(“YOUR_API_KEY_GOES_HERE”)
                .setIsProdEnv(false)
                .setIsCustomerInformationOptional(false)
                .setCustomerName(“John Doe”)
                .setCustomerEmail(“johndoe@test.com“)
                .setEnvironment(“YOUR_ENVIRONMENT_GOES_HERE”) // LeatherBackConstants.Environment.DEV (Dev environment), LeatherBackConstants.Environment.STAGING (Staging environment) or LeatherBackConstants.Environment.PROD
                .setCurrencyCode(LeatherBackConstants.Currency.NGN)
                .setReference(“YOUR_UNIQUE_REF_GOES_HERE”)
                .setChannel(LeatherBackConstants.Payment.Channel.getValue(LeatherBackConstants.Currency.NGN))
                .build()
```
Java Code:
``` java
LeatherbackCheckoutParcel leatherbackCheckout = new LeatherbackCheckoutParcel.Builder()
        .setAmount(20.0)
        .setApiKey(“YOUR_API_KEY_GOES_HERE”)
        .setIsProdEnv(false)
        .setIsCustomerInformationOptional(false)
        .setCustomerName(“John Doe”)
        .setCustomerEmail(“johndoe@test.com“)
        .setCurrencyCode(LeatherBackConstants.Currency.NGN)
        .setEnvironment(“YOUR_ENVIRONMENT_GOES_HERE”) // LeatherBackConstants.Environment.DEV (Dev environment), LeatherBackConstants.Environment.STAGING (Staging environment) or LeatherBackConstants.Environment.PROD
        .setReference(“YOUR_UNIQUE_REF_GOES_HERE”)
        .setChannel(LeatherBackConstants.Payment.Channel.getValue(LeatherBackConstants.Currency.NGN))
        .build();
```

### 3. Initialize Payment

Here is a snippet:

Kotlin Code:
``` kotlin
 CheckoutManager.initialize(
                context,
                leatherbackCheckout,
                startActivityResult,
                onInitializeError = {

                }
            )

```

Java Code:
``` java
CheckoutManager.initialize(
    context,
    leatherbackCheckout,
    startActivityResult,
    new Function0<Unit>() {
        @Override
        public Unit invoke() {
            // onInitializeError callback implementation goes here
            return null;
        }
    }
);

```

### TADA. Possible Return Types on the SDK

 ``` kotlin
    LeatherBackConstants.LeatherBackTransactionStatus.ABORTED -> Payment Gateway Closed Manually
    LeatherBackConstants.LeatherBackTransactionStatus.SUCCESS -> Transaction was successful
    LeatherBackConstants.LeatherBackTransactionStatus.FAILED -> Transaction failed
   ```


### Available Currency Code and their channels 

``` kotlin
    LeatherBackConstants.Currency.NGN  -> LeatherBackConstants.PaymentMethods.Card , LeatherBackConstants.PaymentMethods.BANK_TRANSFER
    LeatherBackConstants.Currency.GBP  -> LeatherBackConstants.PaymentMethods.Card , LeatherBackConstants.PaymentMethods.ACCOUNT
```

### To return all the channels for a currency code

Here is a snippet:
Kotlin code:
``` kotlin
    val leatherbackCheckout = LeatherbackCheckoutParcel
                .Builder()
                .setChannel(LeatherBackConstants.Payment.Channel.getValue(LeatherBackConstants.Currency.GBP))
                .build()
```

Java Code:
``` java
    LeatherbackCheckoutParcel leatherbackCheckout = new LeatherbackCheckoutParcel.Builder()
        .setChannel(LeatherBackConstants.Payment.Channel.getValue(LeatherBackConstants.Currency.GBP))
        .build();
```


### N:B to allow only one of some channel(s) for a currency code
Here is a snippet:

Kotlin Code:
``` kotlin
    val leatherbackCheckout = LeatherbackCheckoutParcel
                .Builder()
                .setChannel(listOf(LeatherBackConstants.PaymentMethods.BANK_TRANSFER))
                .build()
```

Java Code:
``` java
    LeatherbackCheckoutParcel leatherbackCheckout = new LeatherbackCheckoutParcel.Builder()
        .setChannel(Arrays.asList(LeatherBackConstants.PaymentMethods.BANK_TRANSFER))
        .build();
```


