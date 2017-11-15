Integration with Django Rest Framework
======================================

You can re-use your Django Rest Framework serializer with
graphene django.


Mutation
--------

You can create a Mutation based on a serializer by using the
`SerializerMutation` base class:

.. code:: python

    from graphene_django.rest_framework.mutation import SerializerMutation

    class MyAwesomeMutation(SerializerMutation):
        class Meta:
            serializer_class = MySerializer

Add/Update Operations
---------------------

By default ModelSerializers accept add and update operations. To
customize this use the `model_operations` attribute. The update
operation looks up models by the primary key by default. You can
customize the look up with the lookup attribute.

Other default attributes:

`partial = False`: Accept updates without all the input fields.

.. code:: python

    from graphene_django.rest_framework.mutation import SerializerMutation

    class MyAwesomeMutation(SerializerMutation):
        class Meta:
            serializer_class = MySerializer
            model_operations = ['add', 'update']
            lookup_field = 'id'
            partial = False

Overriding Update Queries
-------------------------

Use the method `resolve_serializer_inputs` to override how
updates are queried.

.. code:: python

    from graphene_django.rest_framework.mutation import SerializerMutation

    class MyAwesomeMutation(SerializerMutation):
        class Meta:
            serializer_class = MySerializer

        @classmethod
        def resolve_serializer_inputs(cls, root, info, **input):
            if 'id' in input:
                instance = Post.objects.filter(id=input['id'], owner=info.context.user).first()
                if instance:
                    return {'instance': instance, 'data': input, 'partial': True}

                else:
                    raise http.Http404

            return {'data': input, 'partial': True}
