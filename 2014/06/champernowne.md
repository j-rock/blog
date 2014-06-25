#Champernowne's constant (Project Euler #40 spoiler)

##Project Euler gets hacked
So, recently Project Euler got hacked and they took down the website. Subsequently they put the problems back up (but not user accounts). I chose to do a [random problem](http://projecteuler.net/problem=40) on the first page.

It reads:
> An irrational decimal fraction is created by concatenating the positive integers:
>
> 0.12345678910 __1__ 112131415161718192021...
>
> It can be seen that the 12th digit of the fractional part is 1.
>
> If dn represents the nth digit of the fractional part, find the value of the following expression.
>
>       d1 × d10 × d100 × d1000 × d10000 × d100000 × d1000000


##A tangent
As far as I understand, the List monad bind operation looks like:

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> (>>=) :: [a] -> (a -> [b]) -> [b]
> (>>=) = flip concatMap
>
> -- which is effectively:
> xs >>= f = concatMap f xs
>
> -- I personally like separating concatMap out
> xs >>= f = concat $ map f xs
>
> -- How about some unnecessary point-free crap?
> (>>=) = (concat .) . flip map
>
> -- How about a list comprehension?
> xs >>= f = [x' | x <- xs, x' <- f x]
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The idea is that we take a list and a function that maps list elements to new lists. Then we apply that function over each list element and concatenate each of the new lists produced. To illustrate:

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> -- A list of numbers
> xs :: Num a => [a]
> xs = [1, 3, 5]
>
> -- A function that takes a number and returns a list of new numbers
> f x :: Num a => a -> [a]
> f x = [x + 1, x * 2]
> 
> xs >>= f = concat $ map f xs
>          = concat $ [f 1, f 3, f 5]
>          = concat $ [[1 + 1, 1 * 2], [3 + 1, 3 * 2], [5 + 1, 5 * 2]]
>          = [2,2] ++ [4, 6] ++ [6, 10]
>          = [2, 2, 4, 6, 6, 10]
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

##Back to Champernowne
For this problem, we don't care about the fractional representation of the constant. For me, it just has certain digits at certain indices.

Treat the constant as an infinite string: the concatenation of all the "stringified" natural numbers:

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> -- Bear in mind, a String = [Char]
> champernowne = "01234567891011..."
>              = "0" ++ "1" ++ ... ++ "10" ++ "11" ++ ...
>              = concat $ ["0", "1", ..., "10", "11", ...]
>              = concat $ map f [0..]
>              = [0..] >>= f
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Holy crap: the List monad found its way in here. How are we going to get that function f, though? It has a type signature like:

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> f :: Int -> String
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Oh, that's not so bad. The prelude gives us that:

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> show :: Show a -> a -> String
> f = show 
> champernowne = [0..] >>= show
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After the fun List monad revelation, the actual problem is a little dull. We need to index the constant at a bunch of places and take the product.

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> --we need a way to get a number from the character
> import Data.Char (digitToInt)
>
> --our indices are [1, 10, ..., 1000000]
> indices = take 7 $ iterate (*10) 1
> indexC = (!!) champernowne
> 
> problem40 = product $ map (digit2Int . indexC) indices
> 
> --how about an uglier version?
> problem40' = product $ map (digit2Int . (!!) ([0..] >>= show)) $ take 7 $ iterate (*10) 1
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#### June 25, 2014
