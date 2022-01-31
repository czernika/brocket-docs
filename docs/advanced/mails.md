# Sending Emails

Brocket provides easy way to customize your SMTP-credentials (or use native WordPress `wp_mail()` system) and send email via code

## Using Facade

Brocket comes with `Brocooly\Support\Facades\Mail` facade and it has methods you need

### to( string|array $mailTo )

Set recipient or an array of email recipients

Returns `$this`;

### subject( string $subject )

Set mail subject

Returns `$this`;

### message( string $message )

Set mail text as a string

Returns `$this`;

### template( string|array $template, array $ctx = [] )

Same as `message` but you may specify twig template to use with its context

Returns `$this`;

### headers( $headers = '' )

Set mail headers

Returns `$this`;

### attachments( $attachments = [] )

Add attachments with a mail

Returns `$this`;

### send()

End logic and send email

Returns `boolean` value - `false` if there are some error ocurred. 

```php
Mail::to( 'recipient@mail.com' )
    ->subject( 'Hello World' )
    ->message( 'And this is message body' )
    ->send(); 
```

## Using Mailable

You may use `mailable( $mailer )` method to send email in one line

!> No need to end mailer with `send()` method if you're using ``

### Create Mailable

To create mailable class run

```sh
php brocooly new:mail MailableName
```

This will create `MailableName.php` file within `Theme/Mail` directory

Within `build()` method specify your logic

```php
Mail::mailable( new MailableName() );

class Mailable
{
    public function build()
    {
        return $this->to( 'recipient@mail.com' )
            ->subject( 'Hello World' )
            ->message( 'And this is message body' );
    }
}
```

To use templates provide template name in a dotted notation

```php
Mail::mailable( new MailableName() );

class Mailable
{
    public function build()
    {
        return $this->to( 'recipient@mail.com' )
            ->subject( 'Hello World' )
            ->template( 'path.to.template', [ 'foo' => 'bar' ] );
    }
}
```

## Configuration

Additional mail configuration may be found within `config/mail.php` file and `.env`. To use specific mailer specify it within `MAIL_MAILER` key and add SMTP credentials. To use native WordPress mail system set `MAIL_MAILER=wordpress`