# VEIS API
[Created with ![heart](http://i.imgur.com/oXJmdtz.gif) in Poland by RapidDev](http://rapiddev.pl/)<br />
Simple API to download information about a European entrepreneur using PHP

The basis of the code developed by [Eugen Mihailescu](https://github.com/eugenmihailescu)
***
![PHP version from PHP-Eye](https://img.shields.io/php-eye/symfony/symfony.svg?style=for-the-badge)
***

## Function
```php
function veis($vat)
{
	$vat = preg_replace("/[^A-Za-z0-9?!]/",'',$vat);
	$countryCode = substr($vat,0,2);
	$vatNumber = substr($vat,2,20);
	$response = array();
	$result = file_get_contents('http://ec.europa.eu/taxation_customs/vies/services/checkVatService', false, stream_context_create(array(
		'http' => array(
			'method' => 'POST',
			'header' => 'Content-Type: text/xml; charset=utf-8; SOAPAction: checkVatService',
			'content' => '<s11:Envelope xmlns:s11="http://schemas.xmlsoap.org/soap/envelope/"><s11:Body><tns1:checkVat xmlns:tns1="urn:ec.europa.eu:taxud:vies:services:checkVat:types"><tns1:countryCode>'.$countryCode.'</tns1:countryCode><tns1:vatNumber>'.$vatNumber.'</tns1:vatNumber></tns1:checkVat></s11:Body></s11:Envelope>',
			'timeout' => 30
		)
	)));
	if (preg_match(sprintf('/<(%s).*?>([\s\S]*)<\/\1/', 'checkVatResponse'), $result, $matches))
	{
		foreach (array('name','address','vatNumber','countryCode','requestDate','valid') as $key)
		{
			preg_match(sprintf('/<(%s).*?>([\s\S]*)<\/\1/', $key), $matches [2], $value) && $response[$key] = $value [2];
		}
	}
	return $response;
}
```
