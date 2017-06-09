NAME

WebService::BR::Vindi - Perl low level implementation of the
https://vindi.com.br brazilian payment gateway.

SYNOPSIS

      use WebService::BR::Vindi;

      # Contruct the object
      my $vindi = WebService::BR::Vindi->new(
       api_key => "You API key",
       timeout => 120, # HTTP timeout
       debug   => 1 );

      # Conect to vindi REST API and get the result
      # post/get/put/delete methods always returns a perl HASHREF with the resulting data
      my $subscription = $vindi->get( 'subscriptions/1234' );

      if ( $subscription->{subscription}->{status} eq 'active' ) {
       # Do whatever you need
      }


      # A post method example with error handling
      # First parameter is the URL endpoint name - /customer
      # Second parameter is the POST DATA - which will be automatically JSON encoded and sent to the gateway
      my $customer = $vindi->post(
       'customers',
       {
         "name"          => 'Customer's name',
         "email"         => 'his@email.com',
         "registry_code" => '', 
         "code"          => 'XYZ',
       }
      );


      # Wops! Something went wrong!
      if ( $payment_profile->{ErrorStatus} ) {

       # "ErrorStatus" key try to translate the error to an human readable format whenever possible, or send whatever si possible back.
       warn "Something went terribly wrong while creating the customer: ".$customer->{ErrorStatus};

       # "error" if the vindi's plain error recieved
       warn Data::Dumper::Dumper( $customer->{error} );

      # You will probably want to keep this and INSERT in your database
      } else {
       $customer->{customer}->{id};
      }


      # A little more elaborated example - create a new payment_profile object
      my $payment_profile = $vindi->post(
       'payment_profiles',
       { "holder_name"          => 'Holder Name',
         "card_expiration"      => '12/2012',
         "card_number"          => 'XXXXXXXXXXXXXXXX',
         "card_cvv"             => 'XXX',
         "payment_method_code"  => "credit_card",
         "customer_id"          => $customer->{customer}->{id} # This is the vindi's customer ID we just created before
       }
      );

   
      # Wops! Something went wrong!
      if ( $payment_profile->{ErrorStatus} ) {
       warn "Something went terribly wrong while creating your payment profile: ".$payment_profile->{ErrorStatus};

      # All fine!
      } else {
       # Do whatever you need
       $payment_profile->{payment_profile};
      }

DESCRIPTION

This is a straight brindge to the Vindi.com.br payment gateway API.

METHDOS

  new

    Creates the client object.

    api_key
        You secret Vindi API key. Required.

    debug
        Boolean, optional.

    timeout
        Integer, optional, defaults to 120s

  get

    Make a GET request to Vindi API.

        my $customer = $vindi->get( '/customer/123' )

    endpoint
        URI PATH. Required.

        *** You don't need the "/v1" prefix.

  delete

    Make a DELETE request to Vindi API.

        my $customer = $vindi->delete( '/customer/123' )

    endpoint
        URI PATH. Required. Example: /customer/123

  put
  
    Make a PUT request to Vindi API.

    endpoint
        URI PATH. Required.

  post
  
    Make a POST request to Vindi API. You must specify endpoint as first
    parameter, and a hashref as the second.

        my $customer = $vindi->post( 'customer', { name => '...', ... } )

    endpoint
        URL endpoint. Required.

    data
        A hash to be sent.

SEE ALSO

Please check Vindi's full v1 API docs at http://www.vindi.com.br/

AUTHOR

Diego de Lima, <diego_de_lima@hotmail.com>

SPECIAL THANKS

This module was kindly made available by the https://modeloinicial.com.br/ team.

COPYRIGHT AND LICENSE

    Copyright (C) 2017 by Diego de Lima

    This library is free software; you can redistribute it and/or modify it
    under the same terms as Perl itself, either Perl version 5.10.1 or, at
    your option, any later version of Perl 5 you may have available.
