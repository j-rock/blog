#Using the Applicative style to cut out boilerplate

##Type class tutorials
There are plenty of Haskell tutorials that explain the basic category theoretic type classes.

> Monoid
> Functor
> Applicative
> Arrow
> Monad

When it gets to functors, they introduce the shiny definition.

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> class Functor f where
>   fmap :: (a -> b) -> f a -> f b
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

_fmap_ applies a function to a wrapped value and returns a wrapped result.


Usually the tutorials give some elementary definitions

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> instance Functor Maybe where
>   fmap f (Just x) = Just (f x)
>   fmap f Nothing  = Nothing
>
> instance Functor [] where
>   fmap = map
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

I'd like to point out that the tutorial in LYAH on functors mentions the IO functor.

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> instance Functor IO where
>   fmap f action = do
>     result <- action
>     return (f result)
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


##Stop using the Monad
I'm seeing people doing this all the time.

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> myAction :: IO a
> myAction = do
>   result <- action
>   return (f result)
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Do-notation is pretty sweet, but it is overkill for this use case. Remember the functor instance!

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> myAction = fmap f action
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you don't like seeing the word _fmap_, remember that the Functor class has an infix alias

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> (<$>) = fmap
>
> myAction = f <$> action
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It's just so much prettier. _(<$>)_ helps you separate pure code from effectful code.

Here I use _fmap_ to clean up a simple line counting program.

> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> main :: IO ()
> main = do
>   [f] <- getArgs
>   content <- readFile f
>   let count = length (lines content)
>   print count
>
> main :: IO ()
> main = do
>    [f] <- getArgs
>    print =<< length . lines <$> readFile f
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The separation of pure code from effectful code is striking.

#### Aug 21, 2014
