--- 
title: using boost::optional as a range
date: 03/06/2010
--- 

boost::optional is a great utility. I find more and more uses for it everywhere. However I've started
to realize that it is a more general tool that currently thought. I've been reading about Monads 
and looking a little at Haskell and Scala. For our purposes I like the concept that a Monad is a
container or at least container like. It is a type that wraps another type. Generally there are
multiple instances of the inner type wrapped by the outer type.

So what exactly is boost::optional. Well it behaves a little bit like a container that can contain
exactly 0 or 1 elements. However boost::optional does not support the container concepts like
all the other containers. More specifically it doesn't support the range concepts. Would it be
nice to do something like


    void foo(boost::optional<int> opt_v){
        BOOST_FOREACH(const int & v, opt_v){
            cout << v;
        }
    }

You may say that this is redudant as we could just have easily written.

    void foo(boost::optional<int> opt_v){
        if (opt_v){
            cout << v;
        }
    }

but that is missing the point. Bring in the new range adaptor library introduced in boost 1.43 and
then you can start to do fun things.

    int plus(const int & i){
        return i + 1;
    }
    
    void foo(boost::optional<int> opt_v){
        boost::copy
            ( opt_v | transformed(plus)
            , std::ostream_iterator<int>(std::cout)
            );
    }

The output of

    foo(10)

will be

    11

The neat thing is that I have never explicity tested the parameter opt_v
to see if it valid or boost::none. Treating opt_v as a boost::range allows
me to compose functionality easier than I would have. 

The implementation is not so complicated. boost::range provides all the
boilerplate you really need.

    #include "boost/optional.hpp"
    #include "boost/range.hpp"
    #include <boost/iterator/iterator_facade.hpp>


    template<typename T>
    class optional_iterator
      : public boost::iterator_facade
          < optional_iterator<T>
          , T
          , boost::forward_traversal_tag
          , const T & 
          , std::ptrdiff_t
          >
    {
     public:
        optional_iterator()
          : mVal(NULL),
          done(true)
        {}

        explicit optional_iterator(boost::optional<T> const & opt)
          : mVal(&opt)
          , done(opt == false)
        {}

        optional_iterator<T> & operator++(){
            done = true;
            return *this;
        }

        T const & operator*() const{
            return **mVal;
        }
        T const & operator*() {
            return **mVal;
        }

        bool equal(optional_iterator const & other) const{
            return ((done && other.done) ||
               ( mVal == other.mVal && done == other.done));
        }

     private:
        const boost::optional<T> * mVal;
        bool done;
    };


    namespace boost
    {
        //
        // The required functions. These should be defined in
        // the same namespace as 'Pair', in this case 
        // 
        // in namespace 'Foo'.
        //
        
        template< class T >
        inline optional_iterator<T> range_begin( boost::optional<T>& x )
        { 
            return optional_iterator<T>(x);
        }

        template< class T >
        inline optional_iterator<T> range_begin( const boost::optional<T>& x )
        { 
            return optional_iterator<T>(x);
        }

        template< class T >
        inline optional_iterator<T> range_end( boost::optional<T>& x )
        { 
            return optional_iterator<T>();
        }

        template< class T >
        inline optional_iterator<T> range_end( const boost::optional<T>& x )
        { 
            return optional_iterator<T>();
        }

    }

    template<typename T>
    inline boost::mpl::true_ *
    boost_foreach_is_lightweight_proxy(boost::optional<T> *&, boost::foreach::tag) { return 0; }

    namespace boost
    {

        template<typename T>
        struct range_mutable_iterator< boost::optional<T> >
        {
            typedef optional_iterator<T> type;
        };

        template<typename T>
        struct range_const_iterator< boost::optional<T> >
        {
            typedef optional_iterator<T> type;
        };
    }

So in summary. boost::range can be for more than just wrapping containers. Things you might
not obviously see as a range can be adapted and then be used with the new range adaptor. 
