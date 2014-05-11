--- 
title: concatenating ranges in C++ with boost join
date: 08/07/2010
--- 

boost::join does not copy the two vectors into a new container but generates a
pair of iterators (range) that cover the span of both containers. There will be
some performance overhead but maybe less that copying all the data to a new
container first.

See http://www.boost.org/doc/libs/1_43_0/libs/range/doc/html/range/reference/utilities/join.html

will give you this.

    std::vector<int> v0;
    v0.push_back(1);
    v0.push_back(2);
    v0.push_back(3);

    std::vector<int> v1;
    v1.push_back(4);
    v1.push_back(5);
    v1.push_back(6);

    BOOST_FOREACH(const int & i, boost::join(v0, v1)){
        cout << i << endl;
    }

should give you

    1
    2
    3
    4
    5
    6


