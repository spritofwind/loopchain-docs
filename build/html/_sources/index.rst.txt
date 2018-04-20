.. loopchain Docs documentation master file, created by
   sphinx-quickstart on Fri Apr 13 15:53:15 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

loopchain Documentation
==========================================



..  image:: images/features.png
   :scale: 100 %
   :alt: loopchain Features




loopchain은 효율적인 Smart Contract를 기반으로 실시간 거래를 지원할 수 있는 고성능 블록체인입니다.


이 문서는 크게 3개의 세션과 참조 문서들로 구성되어 있습니다.

* :ref:`about-docs`
* :ref:`setup-docs`
* :ref:`score-docs`


추가적으로 필요한 내용은 참조에 정리하였습니다.

* :ref:`reference-docs`

=====

.. _about-docs:

.. toctree::
   :maxdepth: 2
   :caption: loopchain 소개

   Introduction.md
   structure.md

=====

.. _setup-docs:

.. toctree::
    :maxdepth: 2
    :caption: 설치 Tutorial

    Tutorial.md
    Tutorial_1R1P.md
    Tutorial_1R2P.md
    Configuration.md

=====

.. _score-docs:

.. toctree::
    :maxdepth: 2
    :caption: SCORE(Smart Contract On Reliable Environment)

    score_about.md
    score_env.md
    score_local_dev_env.md
    score_dev_about.md

=====

.. _reference-docs:

.. toctree::
    :maxdepth: 2
    :glob:
    :caption: Reference

    Radiostation-RESTful_API.md
    Peer-RESTful_API.md

design
Theme <https://sphinx-rtd-theme.readthedocs.io/en/latest/>
