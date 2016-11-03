==================================
===== BACKOFFICE DES WEBSITE =====
==================================

/*****
*** @Author: Delrodie AMOIKON
*** @version: 1.0.*
*** @Date: Mercredi 02 Novembre 2016
*****/

Ce projet consiste à concevoir le backoffice de mes sites
Il est composé de 5 tables dans la base ded données dont
  - RUBRIQUE: pour la gestion des categories d'articles
  - ARTICLES: pour la gestion des contenus des rubriques
  - IMAGE: pour la gestion des images d'illustration des articles et de la galerie photo
  - UTILISATEURS: pour la gestion des auteurs des articles
  - GROUPE: pour la gestion des groupe d'utilisateurs

Ainsi nous avons comme MLD
** - [*- RUBRIQUE(titre, description, statut)
         IMAGE(url, alt, statut)
         GROUPE(nom, role)
         UTILISATEUR(nom, login, motpass, mail, contact, #groupe)
         ARTICLE(titre, resume, contenu, tags, date_publication, date_modification, statut, #rubrique, #image, #auteur)
     -*]


1°/ **Implementation du template dans la base**
    Création du layout du backoffice dans App/resources/view
    ** - [*app/Resources/view/layout.html.twig*]

2°/ **Integration du bundle BazingaFakerBundle**
    Insertion dans composer.json
    ** - [*"require": {
              ...
              "willdurand/faker-bundle": "@stable"
          },*]

    Mise a jour du composer
    ** - [*composer update*]

    Activation du Bundle dans AppKernel
    [*if (in_array($this->getEnvironment(), ['dev', 'test'], true)) {
        ...
        $bundles[] = new Bazinga\Bundle\FakerBundle\BazingaFakerBundle();
    }*]

    Configuration dans config.yml
    [*bazinga_faker:
        orm: doctrine
        locale: fr_FR
        entities:*]

3°/ **Gestion de la classe Rurbique**
    Generation de l'entité Rubrique
    ** - [*- php bin/console doctrine:generate:entity AppBundle:Rubrique -*]

    Mise a jour de la base de données
    ** - [*- php bin/console doctrine:schema:update --force -*]

    Generation CRUD de l'entité Rubrique
    ** - [*- php bin/console doctrine:generate:crud AppBundle:Rubrique -*]

    Insertion des valeurs dans la table rubrique
    ** - [*- php bin/console faker:populate -*]

    Mise en page des templates (rubrique/new.html.twig, rubrique/index.html.twig, rubrique/edit.html.twig)

    Modification du form RubriqueType();
    ** - [*- $builder
          ->add('titre', TextType::class, array(
                'attr'  => array(
                    'class' => 'form-control'
                )
          ))
          ->add('description', TextareaType::class, array(
                'attr'  => array(
                    'class' => 'form-control'
                )
          ))
          ->add('statut')
          ; -*]

4°/ **Gestion de la classe Article**
    Generation de l'entité Article
    ** - [*- php bin/console doctrine:generate:entity AppBundle:Article -*]*

    Integration des lifeCycleCallbacks
    ** - [*- * @ORM\HasLifecycleCallbacks -*]
         [*-
             /**
             * @ORM\PrePersist
             */
             public function setPublicationAtValue()
             {
                $this->publicationAt = new \DateTime();
             }

             /**
             * @ORM\PreUpdate
             */
             public function setModificationAtValue()
             {
                $this->modificationAt = new \DateTime();
             }
          -*]

    Mise en relation avec l'entité Rubrique
    ** - [*-
            /**
             * @ORM\ManyToMany(targetEntity="AppBundle\Entity\Rubrique")
             * @ORM\JoinTable(name="articles_rubriques",
             *      joinColumns={@ORM\JoinColumn(name="article_id", referencedColumnName="id")},
             *      inverseJoinColumns={@ORM\JoinColumn(name="rubrique_id", referencedColumnName="id")}
             *      )
             */
             private $rubriques;
         -*]

    Mise a jour de la base de données
    ** - [*- php bin/console doctrine:schema:update --force -*]

    Generation crud de l'entité Article
    ** - [*- php bin/console doctrine:generate:crud AppBundle:Article -*]

    Mise en page de page des différentes views de article

5°/ **Gestion de la classe image**
    Generation de la classe
    ** - [*- php bin/console doctrine:generate:entity AppBundle:Image -*]

    Mise a jour de la base de données
    ** - [*- php bin/console doctrine:schema:update --force -*]

    Generation crud de l'entité Image
    ** - [*- php bin/console doctrine:generate:crud AppBundle:Image -*]

    Modification pour upload de l'image

    Mise en relation Article/Image
    ** - [*-
            /**
             * @ORM\OneToOne(targetEntity="AppBundle\Entity\Image", cascade={"persist", "remove"})
             */
             private $image;
         -*]

    Mise a jour de la classe Article
    ** - [*- php bin/console doctrine:generate:entities AppBundle:Article -*]

    Mise a jour de la base de données
    ** - [*- php bin/console doctrine:schema:update --force -*]

    Integration de classe Image et ImageType dans ArticleType
    ** - [*-  use AppBundle\Entity\Image;
              use AppBundle\Form\ImageType;

              $builder
                    ...
                  ->add('image', ImageType::class)
              ;
        -*]