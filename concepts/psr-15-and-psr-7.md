# PSR-15 and PSR-7

The Borsch Framework follows PSR's recommendations. Therefore:

* Routes must be handled by a [PSR-15 Request Handler](https://www.php-fig.org/psr/psr-15/), taking a 
[PSR-7 Server Request](https://www.php-fig.org/psr/psr-7/) as unique parameter, and must return a
[PSR-7 Response](https://www.php-fig.org/psr/psr-7/).
* Middlewares must be handled by a [PSR-15 Middleware](https://www.php-fig.org/psr/psr-15/), taking a
[PSR-7 Server Request](https://www.php-fig.org/psr/psr-7/)
and a [PSR-15 Request Handler](https://www.php-fig.org/psr/psr-15/) as parameters, and must return a
[PSR-7 Response](https://www.php-fig.org/psr/psr-7/).

Make sure you are confortable with both PSR-7 and PSR-15.