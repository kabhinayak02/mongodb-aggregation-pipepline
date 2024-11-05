### How many users are active? 
```
[
  {
    $match: {
    	isActive: true
  	}
  }
]
```

```
[
  {
    $match: {
    	isActive: true
  	}
  },
  {
    $count: 'activeUserCount'
  }
]
```

### What is the average age of users? 

```
[
  {
    $group: {
      _id: null,
      averageAge:{
        $avg: "$age"
      }
    }
  }
]
```

### What is the average age of users by gender? 
```
[
  {
    $group: {
      _id: "$gender",
      averageAge:{
        $avg: "$age"
      }
    }
  }
]
```

### List the top 2 most common favourite fruits amoung the users with count
```
[
  {
    $group: {
      _id: "$favoriteFruit",
      count: {
        $sum: 1
      }
    }
  },
  {
    $sort: {
      count: -1
    }
  },
  {
    $limit: 2
  }
]
```

### Find the total number of males and females
```
[
  {
    $group: {
      _id: "$gender",
			count: {
        $sum: 1
      }
    }
  }
]
```

### Which Country has the highest number of register users
```
[
  {
    $group: {
      _id: "$company.location.country",
      userCount: {
        $sum: 1
      }
    }
  },
  {
    $sort:{
      userCount: -1
    }
  },
  {
    $limit: 1
  }
]
```

### List all the unique eye colours?
```
[
  {
    $group: {
      _id: "$eyeColor",
      count: {
        $sum: 1
      }
    }
  }
]
```

### What is the average numbers of tags per user?
```
[
  {
    $unwind: "$tags"
  },
  {
    $group: {
      _id: "$_id",
    	numberOfTags: {
        $sum: 1
      }
    }
  },
  {
    $group: {
      _id: "null",
      averageNumbersOfTags: {
        $avg: "$numberOfTags"
      }
    }
  }
]
```

Other way to do using `addFields`
```
[
  {
    $addFields: {
      numberOfTags: {
        $size: {
          $ifNull: ["$tags", []]
        }
      }
    }
  },
  {
    $group: {
      _id: null,
      averageNumberOfTags: {
        $avg: "$numberOfTags"
      }
    }
  }
]
```

### How Many users have "enim" as one of the tags

```
[
  {
    $match: {
      tags: "enim"
    }
  },
  {
    $count: "tagsWithEnim"
  }
]

```

### What are the names and age of users who are inactive and have "velit" as a tag?

```
[
  {
    $match: {
      isActive: false,
      tags: "velit"
    }
  },
  {
    $project: {
      name: 1,
      age: 1
    }
  }
]
```

### How many uses have a phone number starting with '+1 (940)' ? 

```
[
  {
    $match: {
      "company.phone": /^\+1 \(940\)/
    }
  },
  {
    $count: 'usersWithSpecialPhoneNumber'
  }
]
```

### Who has registered the most recently? 

```
[
  {
    $sort: {
      registered: -1
    }
  },
  {
    $limit: 5
  },
  {
    $project: {
      name: 1,
      registered: 1
    }
  }
]

```

### Categorize users by their favorite fruite
```
[
  {
    $group: {
      _id: "$favoriteFruit",
      users: {
        $push: "$name"
      }
    }
  }
]
```

### How many users have "ad" as the second tag in thier list of tags
```
[
  {
    $match: {
      "tags.1": "ad"
    }
  },
  {
    $count: 'tagNameWithAd'
  }
]
```

### Find users who have both "enim" and "id as their tags? 

```
[
  {
    $match: {
      "tags": {
        $all: ["enim", "id"]
      }
    }
  },
  {
    $count: 'TagWithEnimAndId'
  }
]

```

### List all the companies located in the USA with their corresponding user count? 

```
[
  {
    $match: {
      "company.location.country": "USA"
    }
  },
  {
    $group: {
      _id: null,
      userCount: {
        $sum: 1
      }
    }
  }
]

```

### Lookup 

```
[
  {
    $lookup: {
      from: "authors",
      localField: "author_id",
      foreignField: "_id",
      as: "authors_details"
    }
  },
  {
    $addFields: {
      authors_details: {
        $first: "$authors_details"
      }
    }
  }
]
```